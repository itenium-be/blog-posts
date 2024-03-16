---
layout: post
author: Wouter Van Schandevijl
title: ".NET Testing Frameworks"
subTitle: "Comparing NUnit, xUnit and MSTest"
date: 2024-03-13
desc: >
  Cheat sheet comparing testing framework APIs
  for NUnit, xUnit and MSTest.
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
  - git: fluentassertions/fluentassertions
    desc: "Extension methods to fluently assert the outcome of .NET tests"
extras:
  - githubproject: https://github.com/itenium-be/DotNetUnitTestFrameworks
    githubtext: "Example UnitTests in NUnit, xUnit and MSTest"
package-versions:
  - package: NUnit
    version: 4.1.0
  - package: xUnit
    version: 2.7.0
  - package: MSTest.TestFramework
    version: 3.2.2
toc:
  title: .NET Testing Frameworks
  icon: dot-circle-o
---


Comparing .NET Testing Frameworks.

{% include github-stars.html url="xunit/xunit" desc="Community-focused unit testing tool" %}
{% include github-stars.html url="nunit/nunit" desc="NUnit Framework" %}
{% include github-stars.html url="microsoft/testfx" desc="MSTest framework and adapter" %}

<!--more-->

Target audience: A developer switching frameworks :)


# Test Suites

Typically test classes and/or methods are decorated with Attributes.


|                    | NUnit                    | xUnit               | MSTest    |
|--------------------|--------------------------|---------------------|-----------|
| Namespace          | NUnit.Framework          | Xunit               | Microsoft.VisualStudio<br>.TestTools.UnitTesting
| Class Attribute    | [TestFixture][n-class] (optional) |            | TestClass
| Method Attribute   | [Test][n-fn]             | Fact                | TestMethod
| Ignoring           | [Ignore][n-ignore]       | Fact(Skip="")       | Ignore
| **Setup & Teardown**
| Before all tests   | [OneTimeSetUp][n-os]     | IClassFixture\<T\>  | ClassInitialize
| Before each test   | [SetUp][n-up]            | Constructor         | TestInitialize
| After each test    | [TearDown][n-dwn]        | IDisposable.Dispose | TestCleanup
| After all tests    | [OneTimeTearDown][n-ot]  | IClassFixture\<T\>  | ClassCleanup
| **Meta data**
| Description        | [Description][n-desc]    |                     | Description
| Categories         | [Category][n-cat]        |                     | TestCategory
| Custom properties  | [Property][n-prop]       | Trait               | TestProperty
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


# Assertions

```c#
bool? actual = true;

// NUnit
Assert.That(actual, Is.EqualTo(true));

// xUnit
Assert.Equal(true, actual);

// MSTest
Assert.AreEqual(true, actual);
```

## Basic

| NUnit                    | xUnit               | MSTest       | Notes
|--------------------------|---------------------|--------------|------
| Is.EqualTo               | Equal               | AreEqual     | Using IEquatable<T>
| Is.Not.EqualTo           | NotEqual            | AreNotEqual
| Is.Null                  | Null                | IsNull
| Is.True                  | True                | IsTrue
| Is.SameAs                | Same                | AreSame      | Same referenced object
{: .table-code}


## Strings

| NUnit                     | xUnit               | MSTest                           | Notes
|---------------------------|---------------------|----------------------------------|------
| Is.Empty                  | Empty               |
| Does.Contain              | Contains            | StringAssert.Contains
| Does.Not.Contain          | DoesNotContain      |                                  | Only StringAssert.DoesNotMatch for MSTest
| Does.StartWith            | StartsWith          | StringAssert.StartsWith
| Does.EndWith().IgnoreCase | EndsWith            | StringAssert.EndsWith            | xUnit & MSTest use an overload to ignore case
| Does.Match                | Matches             | StringAssert.Matches             | Regex
{: .table-code}


## Exceptions

```c#
Action sut = () => throw new Exception("cause");
Func<Task> asyncSut = () => Task.CompletedTask;

// NUnit
var ex = Assert.Throws<Exception>(() => sut(), "failure message");
Assert.That(ex.Message, Is.EqualTo("cause"));

Assert.DoesNotThrowAsync(() => asyncSut());


// xUnit
var ex = Assert.Throws<Exception>(sut);
Assert.Equal("cause", ex.Message);

Assert.ThrowsAsync<Exception>(() => asyncSut());


// MSTest
// via Attribute
[TestMethod]
[ExpectedException(typeof(Exception))]
public void Exceptions() {}

// Via code
Assert.ThrowsExceptionAsync<Exception>(asyncSut);
```

## Collections

| NUnit                    | xUnit               | MSTest    | Notes
|--------------------------|---------------------|-----------|------
| Is.Empty                 | Empty               |
| Is.EqualTo               | Equal               | CollectionAssert.AreEqual      | Same order
| Is.EquivalentTo          | Equivalent          | CollectionAssert.AreEquivalent | Allow different order
| Has.Some.EqualTo()       | Contains            | CollectionAssert.Contains
| Contains.Item            | Contains            | CollectionAssert.Contains
| Is.Ordered.Descending    |                     |
| Is.All.GreaterThan(1)    | All(col, x => Assert.True(x > 1)) |
| Has.None.Null            | All                 | CollectionAssert.AllItemsAreNotNull
| Has.Exactly(3).Items     |                     |
| Is.SubsetOf              |                     | CollectionAssert.IsSubsetOf
| Is.Unique                |                     | CollectionAssert.AllItemsAreUnique
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
```


## NUnit Only

#### Dates

```c#
Assert.That(later, Is.EqualTo(now).Within(TimeSpan.FromHours(3.0)));
Assert.That(later, Is.EqualTo(now).Within(3).Hours);
```

#### Directories

Also: `Is.SamePath(OrUnder)`

```c#
Assert.That("/folder1/./junk/../folder2", Is.SubPathOf("/folder1/"));
Assert.That(new DirectoryInfo("c:\\temp"), Does.Exist);
Assert.That(new DirectoryInfo("c:\\temp"), Is.Not.Empty);
```

#### And/Or

```c#
Assert.That(3, Is.LessThan(5).Or.GreaterThan(10));
```
