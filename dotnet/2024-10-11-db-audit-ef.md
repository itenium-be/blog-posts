---
layout: post
author: Wouter Van Schandevijl
title: "Database Auditing with EntityFramework and SQL Server"
subTitle: "Keep track of what's happening"
date: 2024-10-26
desc: >
  You want to keep track of who'd done what when
  on your entities.
  <br><br>
  What are your options?
bigimg:
  url: ef-audit-big.png
  prompt: "1982 surveillance state"
  origin: Midjourney
img:
  url: ef-audit.png
  origin: Midjourney
  prompt: "a thorough audit"
categories: dotnet
tags: [tutorial,sql]
extras:
  - githubproject: https://github.com/itenium-be/DbAuditWithEF
    githubtext: "SQL Server & EntityFramework Core: how to audit"
package-versions:
  - package: EntityFramework
    version: 8.0.10
toc:
  title: Db Auditing
  icon: dot-circle-o
socials:
  linkedin: ""
  instagram: ""
  twitter: ""
---

Keeping track of database changes in SQL Server with EntityFramework,
let's go over your options with sample implementations.

[All code with some UnitTests](https://github.com/itenium-be/DbAuditWithEF)
can be found at the Github repository.

<!--more-->

# Option 1: Created & Modified By/On Triggers

Each table has 4 extra columns to keep track of by who/when the record was created
and by who/when the record was last updated. Obviously little EF code is needed for
this solution 😀

```sql
CREATE TRIGGER TRG_Products_UPDATE
ON Products AFTER UPDATE
AS
BEGIN
  SET NOCOUNT ON;

  UPDATE Products
  SET ModifiedBy = SYSTEM_USER, ModifiedOn = GETDATE()
  WHERE Id IN (SELECT Id FROM inserted);
END;
```

The CreatedBy/On columns can be set with a default value for which
some EF configuration is needed:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  // Required since EF Core 7:
  // Without this saving changes will fail
  modelBuilder.Entity<Product>()
    .ToTable(tb => tb.UseSqlOutputClause(false));

  // Set default values for CreatedOn/By
  // Also ignore property changes made in code:
  modelBuilder.Entity<Product>().Property(e => e.CreatedOn)
    .HasDefaultValueSql("GETDATE()")
    .ValueGeneratedOnAdd()
    .Metadata.SetBeforeSaveBehavior(PropertySaveBehavior.Ignore);

  modelBuilder.Entity<Product>().Property(e => e.CreatedBy)
    .HasDefaultValueSql("SYSTEM_USER")
    .ValueGeneratedOnAdd()
    .Metadata.SetBeforeSaveBehavior(PropertySaveBehavior.Ignore);

  // The ModifiedOn/By are set by the trigger but still
  // require some EF configuration:
  modelBuilder.Entity<Product>().Property(e => e.ModifiedOn)
    .ValueGeneratedOnAddOrUpdate()
    .Metadata.SetBeforeSaveBehavior(PropertySaveBehavior.Ignore);

  // We're not using ValueGeneratedOnUpdate() because that
  // would allow a value to be set in code during insertion
  modelBuilder.Entity<Product>().Property(e => e.ModifiedBy)
    .ValueGeneratedOnAddOrUpdate()
    .Metadata.SetBeforeSaveBehavior(PropertySaveBehavior.Ignore);
}
```

## UseSqlOutputClause(false)

Required for all tables that have triggers [since EF Core 7](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/breaking-changes?tabs=v7#sqlserver-tables-with-triggers):

```c#
modelBuilder.Entity<Product>().ToTable(tb => tb.UseSqlOutputClause(false));
```

This reverts the behavior of EF back to that of older versions of EF, a less performant
way of capturing the generated Ids during insertion.


## Pros vs Cons

✅ All changes to the table are captured, not just those done by the application  
⚠️ Changes made by the application are typically all done with the same database user, not the actual user  
⚠️ Need to revert to a less performant way of inserting records, which could be a problem for bulk inserts  
⚠️ This approach just keeps track of the last modification and doesn't tell you **what** has changed  



# Option 2: Created & Modified By/On by EF

Doing exactly the same thing but this time we let EF do the heavy lifting.

## Abstraction: UserProvider

EF somehow needs to know who the current user is, so let's create a little abstraction
and inject the correct implementation depending on the runtime context.


```c#
public interface IUserProvider
{
  string UserName { get; }
}

/// <summary>A simple ASP.NET implementation</summary>
public class HttpUserProvider(IHttpContextAccessor accessor) : IUserProvider
{
  public string UserName => accessor.HttpContext?.User?.Identity?.Name ?? "???";
}
```

Our `DbContext` now needs this `IUserProvider`, something the `dotnet ef` CLI doesn't like.

```c#
public class MyDbContextFactory : IDesignTimeDbContextFactory<MyDbContext>
{
  public MyDbContext CreateDbContext(string[] args)
  {
    return new MyDbContext(new FakeUserProvider());
  }
}
```

## Abstraction: Audit Owned Entity

By having all our entities implement `IAudit` we can configure the `Audit` [ValueObject](https://martinfowler.com/bliki/ValueObject.html)
for all our entities as an [Owned entity](https://learn.microsoft.com/en-us/ef/core/modeling/owned-entities).
This way we don't have 4 properties in all our entities cluttering our intellisense.


```c#
public interface IAudit
{
  Audit Audit { get; }
}

public class Audit
{
  public DateTime CreatedOn { get; set; }
  public string CreatedBy { get; set; } = "";
  public DateTime? ModifiedOn { get; set; }
  public string? ModifiedBy { get; set; }
}
```


## The DbContext Implementation

```c#
public class MyDbContext(IUserProvider userProvider) : DbContext
{
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
    var entities = modelBuilder.Model.GetEntityTypes()
      .Where(entityType => typeof(IAudit).IsAssignableFrom(entityType.ClrType))
      .ToArray();

    foreach (var entityType in entities)
    {
      modelBuilder.Entity(entityType.ClrType)
        .OwnsOne(typeof(Audit), nameof(IAudit.Audit), audit =>
        {
            audit.Property(nameof(Audit.CreatedOn)).HasColumnName(nameof(Audit.CreatedOn));
            audit.Property(nameof(Audit.CreatedBy)).HasColumnName(nameof(Audit.CreatedBy));
            audit.Property(nameof(Audit.ModifiedOn)).HasColumnName(nameof(Audit.ModifiedOn));
            audit.Property(nameof(Audit.ModifiedBy)).HasColumnName(nameof(Audit.ModifiedBy));
        });
    }
  }

  public override int SaveChanges()
  {
    // You may want to override SaveChangesAsync too!
    SetAuditFields();
    return base.SaveChanges();
  }

  private void SetAuditFields()
  {
    var entries = ChangeTracker
      .Entries<IAudit>()
      .Where(e => e.State is EntityState.Added or EntityState.Modified);

    foreach (var entityEntry in entries)
    {
      Audit audit = entityEntry.Entity.Audit;
      if (entityEntry.State == EntityState.Added)
      {
        audit.CreatedOn = DateTime.Now;
        audit.CreatedBy = userProvider.UserName;
        // Make sure a dev hasn't set these
        // But really... You know, just don't set them...
        audit.ModifiedOn = null;
        audit.ModifiedBy = null;
      }
      else
      {
        audit.ModifiedOn = DateTime.Now;
        audit.ModifiedBy = userProvider.UserName;

        // Here we also revert any changes made in code to the CreatedBy/On properties
        // Again, this should go without saying and is probably just overhead...
        var originalAudit = entityEntry.Reference(e => e.Audit).TargetEntry!;
        audit.CreatedBy = originalAudit.OriginalValues.GetValue<string>(nameof(Audit.CreatedBy));
        audit.CreatedOn = originalAudit.OriginalValues.GetValue<DateTime>(nameof(Audit.CreatedOn));
      }
    }
  }
}
```

If you really don't trust your team or something, the github repository
also contains code where the `Audit` only has getters and everything is set using
reflection.


## Pros vs Cons

✅ Captures the actual user that made the request  
⚠️ Changes done directly in the database are not tracked  
⚠️ This approach just keeps track of the last modification and doesn't tell you **what** has changed  



# Option 3: Separate Audit Table with Trigger

We create a separate table and track all changes to all our tables with a trigger.

```sql
CREATE TRIGGER [dbo].[TRG_Products_ALL] ON [dbo].[Products]
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
SET NOCOUNT ON;

-- This trigger also updates the Products table ModifiedOn/By
-- But we don't want to create audit records for that!
IF TRIGGER_NESTLEVEL(OBJECT_ID('dbo.TRG_Products_ALL')) > 1
  RETURN;

IF NOT EXISTS(SELECT 1 FROM deleted) AND NOT EXISTS(SELECT 1 FROM inserted)
  RETURN;

DECLARE @ActionType CHAR(1)
IF EXISTS (SELECT * FROM inserted)
  IF EXISTS (SELECT * FROM deleted)
    SELECT @ActionType = 'U'
  ELSE
    SELECT @ActionType = 'I'
ELSE
  SELECT @ActionType = 'D'

DECLARE @inserted XML, @deleted XML
SET @inserted = (SELECT * FROM inserted FOR XML PATH)
SET @deleted = (SELECT * FROM deleted FOR XML PATH)

DECLARE @tableIds VARCHAR(MAX);
SET @tableIds = (SELECT STRING_AGG(CAST(Id AS VARCHAR(MAX)), ',') FROM (SELECT DISTINCT Id FROM (SELECT Id FROM inserted UNION SELECT Id from deleted) ids) distinctIds)

INSERT INTO [Audit] (TableName, TableIds, ActionType, OldValues, NewValues)
SELECT 'Products', @tableIds, @ActionType, @deleted, @inserted;

IF @ActionType = 'U'
  UPDATE dbo.[Products]
  SET ModifiedOn=CURRENT_TIMESTAMP, ModifiedBy=SUSER_SNAME()
  WHERE [Id] IN (SELECT [Id] FROM inserted);

END;
```

This inserts XML with the changes in Audit.OldValues & NewValues.
If you want to use this approach for many tables, you probably want to abstract this in a function or something 😉


## Pros vs Cons

✅ All changes to the table are captured, not just those done by the application  
✅ This approach keeps track of all modifications  
⚠️ Changes made by the application are typically all done with the same database user, not the actual user  
⚠️ Need to revert to a less performant way of inserting records, which could be a problem for bulk inserts  
⚠️ Huge update statements could overflow some of the Audit columns. This could be fixed by inserting a record for every single TableId



# Option 4: Separate Audit Table with EF

We do exactly the same but with EF doing the heavy lifting.
The Old/NewValues are stored as JSON, not as XML.

## Abstraction: IId

We need to get the db ID to insert it into the Audit table!  
All entities have to implement this interface.

```c#
public interface IId
{
  int Id { get; set; }
}
```


## The DbContext Implementation


```c#
public class MyDbContext(IUserProvider userProvider) : DbContext
{
  public override int SaveChanges()
  {
    // and/or override SaveChangesAsync

    // Keep track of certain things before SaveChanges
    // As we need to capture the generated IDs to insert it into our Audit table
    var changes = ChangeTracker.Entries<IId>()
      .Where(e => e.State is EntityState.Added or EntityState.Modified or EntityState.Deleted)
      .Select(e => new ChangedEntity(e))
      .ToArray();

    // Get the inserted DB ids
    int rowsAffected = base.SaveChanges();

    // Generate (& save!) the Audit records
    SaveAuditRecords(changes);
    return rowsAffected;
  }

  private class ChangedEntity
  {
    public EntityEntry<IId> EntityEntry { get; }
    public EntityState State { get; }
    public (string oldValues, string newValues) Modifications { get; }

    public ChangedEntity(EntityEntry<IId> entityEntry)
    {
      EntityEntry = entityEntry;

      // After the first SaveChanges,
      // the State is reset to Unchanged!
      State = entityEntry.State;

      if (State == EntityState.Modified)
      {
        // After the first SaveChanges,
        // nothing is modified anymore!
        string oldValues = GetValues(entityEntry, true);
        string newValues = GetValues(entityEntry, false);
        Modifications = (oldValues, newValues);
      }
    }

    private static string GetValues(EntityEntry entry, bool originalValues)
    {
      var properties = entry.Properties
        .Where(p => p.IsModified)
        .ToDictionary(p => p.Metadata.Name, p => originalValues ? p.OriginalValue : p.CurrentValue);
      return JsonSerializer.Serialize(properties);
    }
  }

  private void SaveAuditRecords(ChangedEntity[] changes)
  {
    foreach (ChangedEntity change in changes)
    {
      var audit = new EfAudit()
      {
        ModifiedBy = userProvider.UserName,
        ActionType = change.State,
        ModifiedOn = DateTime.Now,
        TableId = change.EntityEntry.Entity.Id,
        TableName = change.EntityEntry.Metadata.GetTableName(),
      };

      switch (change.State)
      {
        case EntityState.Added:
          // Explicit cast to object or it serializes just the IId.Id property
          audit.NewValues = JsonSerializer.Serialize((object)change.EntityEntry.Entity);
          break;

        case EntityState.Deleted:
          audit.OldValues = JsonSerializer.Serialize(change.EntityEntry.OriginalValues.ToObject());
          break;

        case EntityState.Modified:
          audit.OldValues = change.Modifications.oldValues;
          audit.NewValues = change.Modifications.newValues;
          break;
      }

      Audit.Add(audit);
    }

    // Second save to insert the Audit records
    base.SaveChanges();
  }
}
```


If you are doing EventSourcing, you already have this.
It also allows you to implement this in different ways.


## Pros vs Cons

✅ This approach keeps track of all modifications  
✅ Captures the actual user that made the request  
⚠️ Changes done directly in the database are not tracked  



# Conclusion

It depends? 🤷  

- Do you care who has changed what in your tables directly/manually or using other applications?
- Do you care what exactly has changed over time and by whom?

Of course, if you trust no one, you could also implement a combination of triggers and EF to
really track everything 😀
