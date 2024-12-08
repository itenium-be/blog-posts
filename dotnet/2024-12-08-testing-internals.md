---
layout: post
author: Wouter Van Schandevijl
title: "Testing Internal Methods"
subTitle: "Testing internal methods of a class"
date: 2024-12-08
desc: >
  You really shouldn't but...
  <br>
  Sometimes it's just too damn handy, ugh, I mean
  sometimes it just can't be avoided! 😀
bigimg:
  url: internals-big.png
  prompt: "a surgeon face with mask in an operating room"
  origin: Midjourney
img:
  url: internals.png
  origin: Midjourney
  prompt: "testing the internals"
categories: dotnet
tags: [testing]
extras:
  - githubproject: https://github.com/itenium-be/DotNetUnitTestFrameworks
    githubtext: "Example UnitTests in NUnit, xUnit and MSTest"
toc:
  title: Internals
  icon: dot-circle-o
socials:
  linkedin: ""
  instagram: ""
  twitter: ""
---

Typically you want to write tests only for `public` methods.
You want to avoid writing tests for private methods
so that when you change the implementation, your tests
remain green and you can do that ruthless refactoring
without having to worry about introducing new bugs.

<!--more-->

The tests are your safety net you want to rely on!


# When

There are actually many cases where you want to do this:

## Legacy Singletons

> **The Legacy Code Dilemma**  
> When we have to change code, we should have tests in place.<br>
> To put tests in place, we often have to change code.

Your old legacy codebase has something like:  
(yeah, it's probably a lot more convoluted than this)

```c#
public class Singleton
{
  public static readonly Singleton Instance = new();
  private Singleton() { }
}
```

### Refactored Testable Singleton

We introduce an interface, mimicking the original API
and provide an `internal` setter so that we can substitute
it with something else during testing!

```c#
public class Singleton : ISingleton
{
  private static ISingleton _instance = new Singleton();

  public static ISingleton Instance
  {
    get => _instance;
    // We introduced a "seam"!
    internal set => _instance = value;
  }

  private Singleton() {}
}
```

## Limiting the API surface

Inside project X you have some helper classes/functions, a few Extension Methods,
or maybe part of your startup code has some logic in it.

These are all `internal` because you like your projects to have a clean
public API, exposing only those things that external users are interested in,
and hide everything else.

You don't want to make them public, but obviously you do want to write tests
for them!


## When Not

```c#
public class Calculator
{
  public Result Calculate()
  {
    var step1 = Step1();
    var step2 = Step2(step1);
    return Step3(step2);
  }

  internal object Step1() {}
  internal object Step2(object data) {}
  internal Result Step3(object data) {}
}
```

I've seen code like this. Instead of testing the public
`Calculate` method only, there are also UnitTests for
the sub-algorithms, methods which have been made `internal`
only for those UnitTests.  
Maybe even mock out the `Step1-3` when testing the public
method 😲

It's almost always a bad idea; as your implementation and
your tests are now very strongly tied together. It's become hard
to change the sub-parts. Worse, if the requirements change,
`Step1-3` might not make much sense anymore but you're probably
going to endure anyway rather than rewrite all those tests...


# How

This blog post was supposed to be just this but, well,
I got carried away...

## Old Legacy Project Format

Create an `AssemblyInfo.cs` class and add:

```c#
using System.Runtime.CompilerServices;
[assembly: InternalsVisibleTo("Company.Project.UnitTests")]
```


## New SDK-Style Project Format

Add to the `csproj` file:

```xml
<ItemGroup>
  <AssemblyAttribute Include="System.Runtime.CompilerServices.InternalsVisibleTo">
    <_Parameter1>Company.Project.UnitTests</_Parameter1>
  </AssemblyAttribute>
</ItemGroup>
```


# Summary

I could easily remember the `AssemblyInfo` version but
the `csproj` one... not so much.

Ready for copy-pasta now!

Somehow I ended up writing about "high cohesion", "Working Effectively with Legacy Code"
and "encapsulation" instead!
