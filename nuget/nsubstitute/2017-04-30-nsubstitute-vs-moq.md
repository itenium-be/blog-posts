---
layout: post
title:  "NSubstitute tutorial: Moq comparison"
date:   2017-04-30 12:00:00 +0200
categories: dotnet
tags: [net,tutorial,testing]
extras:
  - githubproject: https://github.com/be-pongit/NSubstituteTutorial
    githubtext: The accompanying github project contains all code as UnitTests
---

{% include toc title="NSubstitute vs Moq" icon="dot-circle-o" %}

[Moq][Moq] is probably the most used mocking framework out there at the moment.
The creators of [NSubstitute][NSubstitute] however, craved a mocking framework
with comparable capabilities but with a shorter, more succinct syntax.

They have not failed to do so and I loved the NSubstitute syntax right away.

<!--more-->


```c#
// All examples assume
public interface ICalculator
{
	int Add(int a, int b);
	int Divide(int n, int divisor, out float remainder);
	string Mode { get; set; }
	void SetMode(string mode);
}

Mock<ICalculator> moq = new Mock<ICalculator>();
ICalculator nsub = Substitute.For<ICalculator>();
```



## Basic example

Notice how NSubstitute dropped all the ceremony code required by Moq.

```c#
// Moq
var moq = new Mock<ICalculator>();
moq.Setup(calc => calc.Add(1, 2)).Returns(3);
Assert.AreEqual(3, moq.Object.Add(1, 2));

// NSubstitute
// Saves you a whopping 20 characters :)
var nsub = Substitute.For<ICalculator>();
nsub.Add(1, 2).Returns(3);
Assert.AreEqual(3, nsub.Add(1, 2));
```



## Matching arguments

**Moq**

```c#
// It.
moq.Setup(calc => calc.Add(It.IsAny<int>(), It.Is<int>(b => b % 2 == 0))).Returns(3);

//Moq also has
//- It.IsRegex("", , RegexOptions.IgnoreCase)
//- It.IsInRange(0, 1, Range.Inclusive)
```

**NSubstitute**

```c#
// Arg.
nsub.Add(Arg.Any<int>(), Arg.Is<int>(b => b % 2 == 0)).Returns(3);
```



## Verification

**Moq**

```c#
moq.Object.Add(1, 1);
moq.Object.Add(1, 1);
moq.Verify(calc => calc.Add(1, It.IsAny<int>()), Times.Exactly(2));

// Property
moq.VerifyGet(calc => calc.Mode, Times.Never);
```

**NSubstitute**

```c#
nsub.Add(1, 1);
nsub.Add(1, 1);
nsub.Received(2).Add(1, Arg.Any<int>());

// Property
var requiredAssignmentForCompiler = nsub.DidNotReceive().Mode;
```



## Out and ref

`ref` works in both frameworks exactly the same as `out`.

**Moq**

```c#
float remainder = 0.4F;
moq.Setup(calc => calc.Divide(12, 5, out remainder)).Returns(2);
remainder = 0;

Assert.AreEqual(2, moq.Object.Divide(12, 5, out remainder));
Assert.AreEqual(0.4F, remainder);
```

**NSubstitute**

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



## Exceptions

**Moq**

```c#
moq.Setup(calc => calc.Add(1, 1)).Throws<InvalidOperationException>();
Assert.Throws<InvalidOperationException>(() => moq.Object.Add(1, 1));

moq.Setup(calc => calc.SetMode("HEX")).Throws(new ArgumentException());
Assert.Throws<ArgumentException>(() => moq.Object.SetMode("HEX"));
```

**NSubstitute**

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

So I like NSubstitute.

I find Moqs `Func<T1, T2, ...>` overloads they have provided for `.Return` very useful.
NSubstitute requires some more typing to achieve the same `.Return` capabilities but 
you do get to treat methods and properties alike compared to Moq who's stuck with `VerifyGet`.
Because of this NSubstitute got better discoverability inside an IDE while Moq
continues to struggle with a whole bunch of deprecated ways of doing things.

The drop of `.Setup()` and `.Object` for **all** cases is very win for NSubstitute.



[Moq]: https://github.com/moq/moq4
[NSubstitute]: https://github.com/nsubstitute/nsubstitute
[Moq-Help]: https://github.com/Moq/moq4/wiki/Quickstart
[NSubstitute-Help]: http://nsubstitute.github.io
