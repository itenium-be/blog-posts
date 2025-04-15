---
layout: post
author: Wouter Van Schandevijl
title: ".NET Testing Frameworks"
subTitle: "Comparing NUnit, xUnit, MSTest and TUnit"
date: 2024-03-13
desc: >
  Cheat sheet comparing testing framework APIs
  for NUnit, xUnit, MSTest and TUnit.
bigimg:
  url: net-testing-frameworks-big.png
  prompt: "extensive comparison of three different things"
  origin: Midjourney
img:
  url: net-testing-frameworks.png
  desc: "Battle!"
  origin: Midjourney
  prompt: "three unit testing frameworks battling"
categories: dotnet
tags: [testing,cheat-sheet]
interesting:
  - git: shouldly/shouldly
    desc: "Should testing for .NET—the way assertions should be!"
extras:
  - githubproject: https://github.com/itenium-be/DotNetUnitTestFrameworks
    githubtext: "Example UnitTests in NUnit, xUnit and MSTest"
package-versions:
  - package: NUnit
    version: 4.3.2
  - package: xUnit
    version: 2.9.3
  - package: MSTest.TestFramework
    version: 3.8.2
  - package: TUnit
    version: 0.18.9
last_modified_at: 2025-03-12 00:00:00 +0200
updates:
  - date: 2025-03-12 00:00:00 +0200
    desc: Updated to latest versions. Added TUnit, SoftAssertions, Throws derived, ...
toc:
  title: .NET Testing Frameworks
  icon: dot-circle-o
socials_OLD:
  linkedin: "https://www.linkedin.com/posts/itenium_net-testing-frameworks-activity-7188839330306510849-j-T2"
  instagram: "https://www.instagram.com/p/C6I94pCseXN/?utm_source=ig_web_copy_link&igsh=MzRlODBiNWFlZA=="
  twitter: "https://twitter.com/itenium_be/status/1783073550317813769"
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_itenium-dotnet-testingframeworks-activity-7317849390721757184-pZXq"
  facebook: "https://www.facebook.com/share/p/1DAUQUcr1E/"
  twitter: "https://x.com/itenium_be/status/1912083620451262755"
---


Comparing .NET Testing Frameworks.

{% include github-stars.html url="xunit/xunit" desc="Community-focused unit testing tool" %}
{% include github-stars.html url="nunit/nunit" desc="NUnit Framework" %}
{% include github-stars.html url="microsoft/testfx" desc="MSTest framework and adapter" %}
{% include github-stars.html url="thomhurst/TUnit" desc="A modern, fast and flexible .NET testing framework" %}

<!--more-->

Target audience: A developer switching frameworks :)


# Test Suites

Typically test classes and/or methods are decorated with Attributes.


|                    | NUnit                    | xUnit               | MSTest                                           | TUnit
|--------------------|--------------------------|---------------------|--------------------------------------------------|------
| Namespace          | NUnit.Framework          | Xunit               | Microsoft.VisualStudio<br>.TestTools.UnitTesting | TUnit
| Class Attribute    | [TestFixture][n-class] (optional) |            | TestClass                                        | 
| Method Attribute   | [Test][n-fn]             | Fact                | TestMethod                                       | Test
| Ignoring           | [Ignore][n-ignore]       | Fact(Skip="")       | Ignore                                           | Skip
| **Setup & Teardown**
| Before all tests   | [OneTimeSetUp][n-os]     | IClassFixture\<T\>  | ClassInitialize                                  | Before(Class)
| Before each test   | [SetUp][n-up]            | Constructor         | TestInitialize                                   | Before(Test)
| After each test    | [TearDown][n-dwn]        | IDisposable.Dispose | TestCleanup                                      | After(Test)
| After all tests    | [OneTimeTearDown][n-ot]  | IClassFixture\<T\>  | ClassCleanup                                     | After(Class)
| **Meta data**
| Description        | [Description][n-desc]    |                     | Description                                      | 
| Categories         | [Category][n-cat]        | Trait("Category", "") | TestCategory                                   | Category
| Custom properties  | [Property][n-prop]       | Trait               | TestProperty                                     | Property
{: .table-code}


[n-dwn]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/teardown.html
[n-fn]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/test.html
[n-class]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/testfixture.html
[n-up]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/setup.html
[n-prop]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/property.html
[n-os]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/onetimesetup.html
[n-ot]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/onetimeteardown.html
[n-cat]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/category.html
[n-ignore]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/ignore.html
[n-desc]: https://docs.nunit.org/articles/nunit/writing-tests/attributes/description.html



#### xUnit IClassFixture

Setup in xUnit doesn't work with Attributes!  
xUnit injects the same `XUnitTestsFixture` to the constructor
before running each `[Fact]`.

It's also possible to do setup/teardown for all tests within classes decorated with a
`CollectionAttribute` with `ICollectionFixture<T>`, see [shared-context](https://xunit.net/docs/shared-context)
for more info.

```c#
public class XUnitTestsFixture : IDisposable
{
    public XUnitTestsFixture() { /* BeforeAllTests */ }
    public void Dispose() { /* AfterAllTests */ }
}

public class XUnitTestsWithSetUp : IClassFixture<XUnitTestsFixture>
{
    private readonly XUnitTestsFixture _fixture;

    public XUnitTestsWithSetUp(XUnitTestsFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void Test1() { }
}
```


## Assembly setup

### xUnit

As per the `IClassFixture<T>` example above, the `XUnitTestsFixture`
will be injected in all test classes in the assembly.

```c#
[assembly: AssemblyFixture(typeof(XUnitTestsFixture))]
```

### NUnit

```c#
[SetUpFixture]
public class Config
{
    [OneTimeSetUp]
    public void SetUp() { }

    [OneTimeTearDown]
    public void TearDown() { }
}
```

### MSTest

```c#
[TestClass]
public class MyTestClass
{
    [AssemblyInitialize]
    public static void AssemblyInitialize(TestContext testContext)
    {
        // Or return Task!
    }

    [AssemblyCleanup]
    public static void AssemblyCleanup()
    {
        // Or return Task!
    }
}
```

### TUnit

```c#
public class TUnitSetup
{
  [Before(Assembly)]
  public static async Task AssemblySetup() { }

  [After(Assembly)]
  public static async Task AssemblyTeardown()
  {
    // Runs after the last test in GetType().Assembly
  }

  [AfterEvery(Assembly)]
  public static async Task AllAssembliesTeardown(AssemblyHookContext context)
  {
    // Runs after the last test in all assemblies
    // For all Before/After there exists a Before/AfterEvery
    // Aside from Test/Class/Assembly there also is TestSession/TestDiscovery
  }
}
```


# Assertions

```c#
bool? actual = true;

// NUnit
Assert.That(actual, Is.EqualTo(true));

// xUnit
Assert.Equal(true, actual);

// MSTest
Assert.AreEqual(true, actual);

// TUnit
await Assert.That(actual).IsEqualTo(true);
```

## Basic

| NUnit                    | xUnit               | MSTest       | TUnit             | Notes
|--------------------------|---------------------|--------------|-------------------|------
| Is.EqualTo               | Equal               | AreEqual     | IsEqualTo         | Using IEquatable<T>
| Is.Not.EqualTo           | NotEqual            | AreNotEqual  | IsNotEqualTo      |
| Is.Null                  | Null                | IsNull       | IsNull
| Is.True                  | True                | IsTrue       | IsTrue
| Is.SameAs                | Same                | AreSame      | IsSameReferenceAs | Same referenced object
{: .table-code}


## Strings

All but NUnit use an overload to ignore case for Start/End with.

| NUnit                     | xUnit               | MSTest                           | TUnit          |
|---------------------------|---------------------|----------------------------------|----------------|
| Is.Empty                  | Empty               |                                  | IsEmpty        |
| Does.Contain              | Contains            | StringAssert.Contains            | Contains       |
| Does.Not.Contain          | DoesNotContain      | StringAssert.DoesNotMatch        | DoesNotContain |
| Does.StartWith            | StartsWith          | StringAssert.StartsWith          | StartsWith
| Does.EndWith().IgnoreCase | EndsWith            | StringAssert.EndsWith            | EndsWith       |
| Does.Match                | Matches             | StringAssert.Matches             | Matches        |
{: .table-code}


## Exceptions

```c#
Action sut = () => throw new Exception("cause");
Func<Task> asyncSut = () => throw new Exception("cause");

// NUnit
var ex = Assert.Throws<Exception>(() => sut(), "failure message");
Assert.That(ex.Message, Is.EqualTo("cause"));

var ex2 = Assert.ThrowsAsync<Exception>(() => asyncSut());
Assert.That(ex2.Message, Is.EqualTo("cause"));


// xUnit
var ex = Assert.Throws<Exception>(sut);
Assert.Equal("cause", ex.Message);

var ex2 = await Assert.ThrowsAsync<Exception>(() => asyncSut());
Assert.Equal("cause", ex2.Message);


// MSTest
// DEPRECATED: ExpectedExceptionAttribute(typeof(Exception))
// DEPRECATED: ThrowsException<T>()
var ex = Assert.ThrowsExactly<Exception>(sut);
Assert.AreEqual("cause", ex.Message);

var ex2 = await Assert.ThrowsExactlyAsync<Exception>(asyncSut);
Assert.AreEqual("cause", ex2.Message);


// TUnit
// Also: .WithMessageMatching and .WithInnerException
await Assert.That(sut).ThrowsExactly<Exception>().WithMessage("cause");

await Assert.That(asyncSut)
  .ThrowsExactly<ArgumentException>()
  .WithMessageMatching(StringMatcher.AsRegex("^cause$"));
```

### Exact vs Derived

The above assertions expect the exceptions to be of exactly the type
provided. If you do not want a failure when the actual type is a
derived type:

- xUnit: `Assert.ThrowsAny(Async)<T>`
- NUnit: `Assert.Catch(Async)<T>`
- MSTest: `Assert.Throws(Async)<T>`
- TUnit: `var ex = Assert.Throws(Async)<T>(sut)`


## Collections

| NUnit                    | xUnit               | MSTest                             | TUnit          | Notes
|--------------------------|---------------------|------------------------------------|----------------|------
| Is.Empty                 | Empty               | Assert.IsEmpty                     | IsEmpty
| Is.EqualTo               | Equal               | CollectionAssert.AreEqual          | IsEquivalent (overload) | Same order
| Is.EquivalentTo          | Equivalent          | CollectionAssert.AreEquivalent     | IsEquivalent   | Allow different order
| Has.Some.EqualTo()       | Contains            | CollectionAssert.Contains          | Contains
| Contains.Item            | Contains            | CollectionAssert.Contains
| Is.Ordered.Descending    |                     |                                    | IsInOrder<br>IsInDescendingOrder
| Is.All.GreaterThan(1)    | All(col, x => Assert.True(x > 1)) |                      | ContainsOnly
| Has.None.Null            | All                 | CollectionAssert<br>.AllItemsAreNotNull
| Has.Exactly(3).Items     |                     | Assert.HasCount
| Is.SubsetOf              |                     | CollectionAssert.IsSubsetOf
| Is.Unique                |                     | CollectionAssert<br>.AllItemsAreUnique | HasDistinctItems
{: .table-code}

#### xUnit Assert.Collection

Specific assertions per element in the collection.

```c#
Assert.Collection(
  collection,
  el1 => Assert.Null(el1),
  el2 => Assert.False(el2),
  ...
);
```

#### NUnit Dictionary Assertions

```c#
var dict = new Dictionary<int, int>
{
  { 1, 4 },
  { 2, 5 }
};

Assert.That(dict, Does.ContainKey(1).WithValue(4));

Assert.That(dict, Contains.Value(4));
Assert.That(dict, Does.ContainValue(5));
Assert.That(dict, Does.Not.ContainValue(3));
```


## Numbers

```c#
// xUnit
// Only InRange
Assert.InRange(actual, 0, 100);

// NUnit
Assert.That(actual, Is.InRange(0, 100));
Assert.That(actual, Is.AtMost(0));
// Also: AtLeast, Zero, LessThanOrEqualTo, Negative/Positive
// Greater/LessThan uses IComparable

// MSTest: N/A

// TUnit
await Assert.That(actual).IsBetween(0, 100);
await Assert.That(actual).IsLessThan(0);
// Also: IsGreaterThan, IsNotLessThanOrEqualTo
// Is(Not)Zero, IsNegative/Positive
```

## Soft Assertions

A test stops running as soon as an assertion fails. But sometimes it is handy
that all assertions still run so that you have more context to pinpoint the exact
problem. Both xUnit and NUnit have a mecanism to do this.

```c#
// xUnit
Assert.Multiple(
  () => Assert.NotEqual(12, 24),
  () => Assert.NotNull("Hello world")
);

// NUnit
using (Assert.EnterMultipleScope())
{
  Assert.That(12, Is.Not.EqualTo(24));
  Assert.That("Hello world", Is.Not.Null);
}

// TUnit
// Both NUnit & TUnit can also use block scoped using
using var _ = Assert.Multiple();
await Assert.That(12).IsNotEqualTo(24);
await Assert.That("Hello world").IsNotNull();
```

Before NUnit 4.2 the syntax was:
[`Assert.Multiple(() => { /* assertions */ })`](https://docs.nunit.org/articles/nunit/writing-tests/assertions/multiple-asserts.html)


## NUnit and TUnit Only

#### Dates

```c#
// NUnit
Assert.That(later, Is.EqualTo(now).Within(TimeSpan.FromHours(3.0)));
Assert.That(later, Is.EqualTo(now).Within(3).Hours);

// TUnit
await Assert.That(later).IsAfter(now);
await Assert.That(later).IsBetween(now, now.AddHours(3));
```

#### Directories

Also: `Is.SamePath(OrUnder)`

```c#
// NUnit only
Assert.That("/folder1/./junk/../folder2", Is.SubPathOf("/folder1/"));
Assert.That(new DirectoryInfo("c:\\temp"), Does.Exist);
Assert.That(new DirectoryInfo("c:\\temp"), Is.Not.Empty);
```

#### And/Or

```c#
// NUnit
Assert.That(3, Is.LessThan(5).Or.GreaterThan(10));

// TUnit
await Assert.That(3).IsLessThan(5).Or.IsGreaterThan(10);
```

**TUnit**: For more complicated AND/OR logic, check
[AssertionGroup.For](https://thomhurst.github.io/TUnit/docs/tutorial-assertions/assertion-groups)
