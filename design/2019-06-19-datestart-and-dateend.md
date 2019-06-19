---
layout: post
author: Wouter Van Schandevijl
title: "DateStart + DateEnd"
subTitle: "And Their Evil Relatives"
date: 2019-06-19
bigimg: 
  url: valueobjects-big.png
  desc: "Photo by freestocks.org"
  origin: https://unsplash.com/photos/y_dCjaRWthY
img:
  url: valueobjects.jpg
  desc: "Photo by Seth Macey"
  origin: https://unsplash.com/photos/QZBcFvQFmCs
categories: design
tags: [war-story]
interesting:
  - url: https://www.martinfowler.com/books/eaa.html
    desc: "Patterns of Enterprise Application Architecture - Martin Fowler"
  - url: https://docs.microsoft.com/en-us/ef/core/modeling/owned-entities
    desc: "EF Core Owned Entities"
toc:
  title: 🙈 See No Evil
---

# ValueObjects
{: .hide-from-excerpt}

A ramble against objects containing `DateStart` & `DateEnd` pairs of properties.

Some of their equally evil relatives:  
- `DateStart` & `TimeSpan` or `Day/Month only in DateTime`, etc.
- `decimal Money` & `string Currency`
- `decimal X/Y` and/or `Width/Height`


This is the case for the [**ValueObject**](https://martinfowler.com/bliki/ValueObject.html):  

> _A small simple object, like money or a date_  
> _range, whose equality isn't based on identity._  
> — Martin Fowler


<!--more-->

# Basic Example

Consider the following class:
```c#
class Person {
    public string FirstName;
    public string LastName;
    public string Initials;
    public string Address;
    public string Street;
    public int HouseNumber;
    public string Telephone;
    public string Mobile;
    public string Email;
    public string WorkAddress;
    public string WorkStreet;
    public string WorkHouseNumber;
    public DateTime ActiveStart;
    public DateTime? ActiveEnd;
    // ... and many more?
}
```

And how beautiful it could've been:
```c#
class Person {
    // The first version was quite obfuscated, while in this version it's
    // quite obvious what's in it by merely glancing at the declaration.
    public Name Name;
    public Contact Contact;
    public Address HomeAddress;
    public Address WorkAddress;
    public DateRange Active;
}

class Name {
    // Please don't repeat the Name for each property here
    // Considering the current class, we know the context!
    public string First;
    public string Last;
    public string Initials;
}
```

# Advantages

#### For the IDE

Instead of a long list of properties that may be difficult to navigate in your IDE,
where it is difficult to spot that one property you need,
one could instead split it up in things that logically belong together to keep intellisense small and focused.  
Keep data that belongs together - together!


#### For the UI

Such stuff is usually displayed together. All the way down to the frontend, passing
an `Address` to an `AddressComponent` is very preferable over passing those properties
in separately.


#### For Expressiveness

Without our precious `ValueObject`, we would end up creating all sorts of helper classes
for often sought after functionality, all scattered around the codebase, some perhaps
getting duplicated by different programmers or even for different UserStories.

Some of these helper methods would be much better off being members of a `ValueObject`
instead. We get better IDE dicoverability and rather than having to pass some or all properties of an entity into an algorithm,
we invoke a well-named method on the ValueObject in true [TellDontAsk](https://pragprog.com/articles/tell-dont-ask) style.

And isn't that what Object Oriented Design is all about? 😇



# Recognition

If you look, `ValueObject` opportunities will present themselves.

## Technical ValueObjects

Classes are very technical in nature and so there'll be purely technical candidates
like, for example, a (still?) popular one:  
`CreatedOn + CreatedBy` and `UpdatedOn + UpdatedBy`.  
Why clutter (all) your entities with 4 superfluous properties if you can hide them behind
a single `Audit` ValueObject?


## Domain ValueObjects

It is the `ValueObjects` derived from the Business Domain that often offer the
most value however. Helper functions for a `DateRange` only go as long as a `IsIn(DateTime)`.
For Domain ValueObjects, this can be whatever rabbit Marketing pulls out of their hat
depending on the current lunar phase, so the possibilities are endless!


### Not Invented Here Syndrome

I never thought I'd ever say this: Do not use a package. Yes, there is that well-known and fully-fledged `DateRange` package out there.
80% of its features are probably not useful for your problem domain though. Again you get a long list of methods that basically
clutter your IDE. Super handy features that are applicable only in your domain will most likely not be available, as your business
probably has very particular requirements.


### Business Domain Example

```c#
// A domain where we work with measured stuff
class MeasurementResults {
    public decimal Value1;
    public int Value1Precision;
    public decimal Value2;
    public int Value2Precision;
    public decimal Value3;
    public int Value3Precision;
}

// Could be turned into
class MeasurementResults {
    public Measurement Result1;
    public Measurement Result2;
    public Measurement Result3;
}

class Measurement {
    public decimal Value;
    public int Precision;
}
```




# Random Thoughts

#### The True Enemy?

Well I guess that missing `ValueObjects` are often an unfortunate side-effect of using a relational database.
Your SQL database simply likes things flat. Your ORM probably has something like Custom Types (Hibernate), 
Complex Types (EF) or Owned Entities (EF Core). If you use an ORM it can probably do the ValueObject creation
for you.


#### Derive, Don't Store

If you can calculate something from existing state, do so.
Once stored separately, it will, at some point be out of sync, I guarantee it.

```c#
class Person {
    public string FirstName;
    public string LastName;

    // This simply does not get out of sync
    // Otherwise each day is trying to remember if the
    // FullName starts or ends with the FirstName
    // + You have less state to manage, so huray!
    public string FullName => $"{FirstName} {LastName}";
}
```


# Conclusions


Not creating a value object for coherent pairs of value types is a missed opportunity? 😂
