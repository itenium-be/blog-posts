---
layout: post
author: Wouter Van Schandevijl
title:  "NSubstitute Tutorial"
date:   2018-08-12 12:00:00 +0200
categories: dotnet
desc: >
    Tutorial for the mocking framework with
    the prettiest, most succinct syntax.
img: nsubstitute-alt.png
tags: [net,tutorial,testing]
series: nsubstitute
extras:
  - githubproject: https://github.com/itenium-be/NSubstituteTutorial
    githubtext: The accompanying github project contains all code as UnitTests
toc:
    title: NSubstitute Tutorial
    icon: dot-circle-o
---

The creators of [NSubstitute][NSubstitute] craved a mocking framework
with comparable capabilities to the alternatives but with a shorter, more succinct syntax.

They have not failed to do so and I loved the NSubstitute syntax right away.

<!--more-->

<br>
Get started right away with  
```
Install-Package NSubstitute
```

<br>
All examples assume  
```c#
public interface ICalculator
{
    int Add(int a, int b);
    int Divide(int n, int divisor, out float remainder);
    string Mode { get; set; }
    void SetMode(string mode);
}

ICalculator nsub = Substitute.For<ICalculator>();
```



# Basic example

```c#
var nsub = Substitute.For<ICalculator>();
nsub.Add(1, 2).Returns(3);
Assert.AreEqual(3, nsub.Add(1, 2));
```



# Matching arguments

```c#
nsub.Add(Arg.Any<int>(), Arg.Is<int>(b => b % 2 == 0)).Returns(3);
```


# Verification

```c#
nsub.Add(1, 1);
nsub.Add(1, 1);
nsub.Received(2).Add(1, Arg.Any<int>());

// Property
var requiredAssignmentForCompiler = nsub.DidNotReceive().Mode;
```



# Out and ref

`ref` works in the same way as `out`.

```c#
float remainder;
nsub.Divide(12, 5, out remainder).Returns(x => {
  x[2] = 0.4F; // [2] = 3th parameter = remainder
  return 2;
});
remainder = 0;

Assert.AreEqual(2, nsub.Divide(12, 5, out remainder));
Assert.AreEqual(0.4F, remainder);
```



# Exceptions

```c#
using NSubstitute.ExceptionExtensions;

nsub.Add(1, 1).Throws(new Exception());
Assert.Throws<InvalidOperationException>(() => nsub.Add(1, 1));

nsub.When(x => x.SetMode("HEX")).Throw<Exception>();
Assert.Throws<ArgumentException>(() => nsub.SetMode("HEX"));

// The extension method syntax is much cleaner
// Without it, the setup becomes the following
nsub.Add(1, 1).Returns(x => { throw new InvalidOperationException(); });
nsub.When(x => x.SetMode("HEX")).Do(x => { throw new ArgumentException(); });
```



# Summary

So I like NSubstitute. Like alot.



[NSubstitute]: https://github.com/nsubstitute/nsubstitute
[NSubstitute-Help]: http://nsubstitute.github.io
