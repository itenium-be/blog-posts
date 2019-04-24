---
layout: post
author: Wouter Van Schandevijl
title:  "NSubstitute Tutorial"
date:   2018-08-12 12:00:00 +0200
categories: dotnet
desc: >
    Tutorial for the mocking framework with
    the prettiest, most succinct syntax.
img:
  url: nsubstitute-alt.png
tags: [tutorial,testing]
series: nsubstitute
url-short: https://bit.ly/2MdZf7n
extras:
  - githubproject: https://github.com/itenium-be/NSubstituteTutorial
    githubtext: The accompanying github project contains all code as UnitTests
toc:
    title: NSubstitute Tutorial
    icon: dot-circle-o
package-versions:
  NSubstitute: 2.0.3
---

The creators of [NSubstitute][NSubstitute] craved a mocking framework
with comparable capabilities to the alternatives but with a shorter, more succinct syntax.
They have not failed to do so and I loved the NSubstitute syntax right away.

<!--more-->

<br>
The examples assume

```c#
// Packages:
Install-Package NSubstitute
Install-Package NUnit

// Interface:
interface ICalculator
{
    int Add(int a, int b);
    int Divide(int n, int divisor, out float remainder);
    string Mode { get; set; }
    void SetMode(string mode);
}
```


# Creating a substitute

```c#
// For an interface
ICalculator nsub = Substitute.For<ICalculator>();

// For a class
Calculator nsub = Substitute.For<Calculator>(ctorArg1, ctorArg2);
```

When substituting a class, only virtual/abstract members can be mocked.  
There are generic overloads for up to three different types. When
providing multiple types, max one class can be used. For more than three:

```c#
var sub = Substitute.For(
    new[] { typeof(IComparable), typeof(IDisposable), typeof(ICloneable), typeof(Calculator) },
    new object[] { /* ctor arguments for Calculator */ }
);
Assert.IsInstanceOf<IDisposable>(sub);
Assert.IsInstanceOf<Calculator>(sub);
```



# Matching arguments

```c#
// Arg.Any and fixed value
nsub.Add(Arg.Any<int>(), 5).Returns(10);
Assert.That(nsub.Add(999, 5), Is.EqualTo(10));

// Arg.Is: Fixed value and Predicate
nsub.Add(Arg.Is(1), Arg.Is<int>(x => x < 0)).Returns(5);
Assert.That(nsub.Add(1, -2), Is.EqualTo(5));

// First matcher still applies
Assert.That(nsub.Add(998, 5), Is.EqualTo(10));

// ReturnsForAnyArgs overwrites everything
nsub.Add(0, 0).ReturnsForAnyArgs(100);
Assert.That(nsub.Add(1, -2), Is.EqualTo(100));
```


# Verification

**Did receive calls:**

```c#
// Methods
nsub.Add(0, 1);
nsub.Received().Add(0, 1);
nsub.ReceivedWithAnyArgs().Add(0, 0);

// Property Getter
string call = nsub.Mode;
string result = nsub.Received().Mode;

// Property Setter
nsub.Mode = "BIN";
nsub.Received().Mode = "BIN";

// Reset calls
nsub.ClearReceivedCalls();

// Verify with predicate
nsub.Add(0, 1);
nsub.Add(0, 2);
nsub.Add(0, 3);
nsub.Received(2).Add(Arg.Is(0), Arg.Is<int>(x => x <= 2));
// In this case the Arg.Is(0) is required by NSubstitute
// (providing 0 directly results in an AmbiguousArgumentsException)
```

**Did not receive calls:**

```c#
nsub.DidNotReceive().Add(Arg.Any<int>(), 9);

// The actual arguments to Add are simply ignored with DidNotReceiveWithAnyArgs
nsub.DidNotReceiveWithAnyArgs().Add(0, 0);
```


# Providing values

Without setup the following is returned:  
- For primitives: `default(int)`
- For strings: `string.Empty`
- For IEnumerables: `Enumerable.Empty<T>()`
- For interfaces: a `Substitute.For<T>()`, the same reference when called with the same arguments

When substituting a class, non-virtual methods returning an interface
cannot be mocked and will return `null`.


## Methods and properties

```c#
// Methods
nsub.Add(1, 3).Returns(4);
Assert.That(nsub.Add(1, 3), Is.EqualTo(4));
Assert.That(nsub.Add(1, 3), Is.EqualTo(4));

// Properties
nsub.Mode = "HEX";
Assert.That(nsub.Mode, Is.EqualTo("HEX"));

// Can also use the same syntax as for methods:
nsub.Mode.Returns("DEC");
Assert.That(nsub.Mode, Is.EqualTo("DEC"));


// Provide multiple return values
nsub.Mode.Returns("DEC", "HEX", "BIN");
Assert.AreEqual("DEC", nsub.Mode);
Assert.AreEqual("HEX", nsub.Mode);
Assert.AreEqual("BIN", nsub.Mode);

// Multiple return values with a function
nsub.Mode.Returns(
    x => "DEC",
    x => "HEX",
    x => throw new Exception()
);
Assert.AreEqual("DEC", nsub.Mode);
Assert.AreEqual("HEX", nsub.Mode);
Assert.Throws<Exception>(() => { var result = nsub.Mode; });
```


### For all of type

```c#
using NSubstitute.Extensions;

nsub.Add(1, 3).Returns(3);
nsub.ReturnsForAll<int>(5);

Assert.That(nsub.Add(1, 3), Is.EqualTo(3));
Assert.That(nsub.Add(0, 9), Is.EqualTo(5));
Assert.That(nsub.Divide(0, 9, out float remainder), Is.EqualTo(5));
```


### Out and ref

`ref` works in the same way as `out`.

```c#
nsub.Divide(12, 5, out float remainder).Returns((CallInfo callInfo) => {
  callInfo[2] = 0.4F; // [2] = 3th parameter = remainder
  return 2;
});

Assert.AreEqual(2, nsub.Divide(12, 5, out remainder));
Assert.AreEqual(0.4F, remainder);
```


## Exceptions

```c#
using NSubstitute.ExceptionExtensions;

nsub.Add(1, 1).Throws(new Exception());
Assert.Throws<Exception>(() => nsub.Add(1, 1));

nsub.When(x => x.SetMode("HEX")).Throw<Exception>();
Assert.Throws<Exception>(() => nsub.SetMode("HEX"));
```


# Side effect callbacks

**AndDoes**:  
Do something after `Add` was called.

```c#
int counter = 0;
nsub
    .Add(0, 0)
    .ReturnsForAnyArgs(x => 0)
    .AndDoes(x => counter++);

nsub.Add(7, 3);
nsub.Add(2, 2);
Assert.AreEqual(counter, 2);
```


**Make voids do something**:  

```c#
bool called = false;
sub
    .When(x => x.SetMode("HEX"))
    .Do(x => called = true);

sub.SetMode("HEX");
Assert.True(called);
```


**Do something with arguments passed**:  

```c#
int argumentUsed = 0;
nsub.Add(10, Arg.Do<int>(x => argumentUsed = x));

nsub.Add(10, 42);
nsub.Add(11, 0); // does not overwrite argumentUsed because first arg is not 10

Assert.AreEqual(42, argumentUsed);
```



# Less frequently used


## Partial Class Substitution

```c#
public class SummingReader
{
    /// <summary>
    /// We will actually execute this
    /// </summary>
    public virtual int CalculateSum(string path)
    {
        var s = ReadFile(path);
        return s.Split(',').Select(int.Parse).Sum();
    }

    /// <summary>
    /// While substituting this behavior
    /// </summary>
    public virtual string ReadFile(string path)
    {
        throw new Exception($"Actually attempted to access '{path}' on filesystem!");
    }
}

// Tests:
var reader = Substitute.ForPartsOf<SummingReader>();

// Without Arg. matchers, the actual ReadFile will be executed
Assert.Throws<Exception>(() => reader.ReadFile("foo.txt").Returns("1,2,3"));

// The Arg.Is makes sure that ReadFile is not executed
reader.ReadFile(Arg.Is("foo.txt")).Returns("1,2,3");
int result = reader.CalculateSum("foo.txt");
Assert.That(result, Is.EqualTo(6));

// Alternatively: Use DoNotCallBase to play it (a bit) safer.
// Make sure the ReadFile call won't call real implementation
reader = Substitute.ForPartsOf<SummingReader>();
reader.When(x => x.ReadFile("foo.txt")).DoNotCallBase(); // <-- Magic here
reader.ReadFile("foo.txt").Returns("1,2,3");
result = reader.CalculateSum("foo.txt");
Assert.That(result, Is.EqualTo(6));
```

## Provide values using CallInfo callback

```c#
nsub
    .Add(Arg.Any<int>(), Arg.Any<int>())
    .Returns((CallInfo callInfo) =>
    {
        // Get argument value with indexer
        int firstArg = (int)callInfo[0];

        // By position
        int secondArg = callInfo.ArgAt<int>(1);

        // By type
        // Throws because there should be a Single parameter of the type
        Assert.Throws<AmbiguousArgumentsException>(() => callInfo.Arg<int>());

        // Get parameter type information
        Assert.That(callInfo.ArgTypes().First(), Is.EqualTo(typeof(int)));

        // Actual return value
        return firstArg + secondArg;
    });

Assert.That(nsub.Add(1, 3), Is.EqualTo(4));

```



## Events

- [Checking event subscriptions](http://nsubstitute.github.io/help/received-calls/#checking_event_subscriptions)
- [Checking event invocation](http://nsubstitute.github.io/help/received-calls/#checking_event_invocation)
- [Raising events](http://nsubstitute.github.io/help/raising-events)

# Summary

So I like NSubstitute. Like alot. That's it.

For more examples - as UnitTests - check the [Github source][github-source].


[NSubstitute]: https://github.com/nsubstitute/nsubstitute
[github-source]: https://github.com/itenium-be/NSubstituteTutorial
