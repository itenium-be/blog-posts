---
layout: post
author: Wouter Van Schandevijl
title: "KISS and Decorators"
subTitle: "Decorators: a powerful and often misused design pattern"
date: 2024-09-03
desc: >
  More often than not I see the Decorator pattern being used
  while using a different Design Pattern, or even none at all
  would have resulted in less and easier to understand code.
bigimg:
  url: kiss-decorators-big.png
  origin: Midjourney
  prompt: "workers decorating the interior of a home"
img:
  url: kiss-decorators.png
  desc: "Stay wary of decorators but they should be in your toolbox"
  origin: Midjourney
  prompt: "kiss"
categories: design
tags: [war-story]
extras:
  - githubproject: https://github.com/itenium-be/KISS-Decorators
    githubtext: "The code from this blog post as xUnit tests"
toc:
  title: 🎁 Keep It Simple
  icon:
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_itenium-keeponlearning-itdevelopment-activity-7240298396052312064-PnP3"
  instagram: "https://www.instagram.com/p/C_2muHtNNw5/"
  twitter: "https://x.com/itenium_be/status/1834532573394931816"
  facebook: "https://www.facebook.com/share/p/emS3YtJeu9hbehat/?mibextid=oFDknk"
---

I picked up a UserStory to make a small adjustment.

The story was simple enough as was the required change
but... Looking at the code, how was it
supposed to work? It looked like it just.. Shouldn't...

Took some time but I found out, the behavior of the software was
defined in the IOC registration, at startup, with Autofac.

<!--more-->

# The Implementation

A simplified version of the code would be:

```c#
public interface IDoer
{
  void DoIt();
}


public class Doer : IDoer
{
  public void DoIt()
  {
    // Actual Business Logic here
    // But... Only happening for
    // one case?
  }
}
```

And then another implementation, a decorator:

```c#
public class LoopingDoer : IDoer
{
  private readonly IDoer _inner;

  public LoopingDoer(IDoer inner)
  {
    _inner = inner;
  }

  public void DoIt()
  {
    // We were looping over something
    // that made sense here, not just
    // this for loop:
    for (int i = 0; i < 10; i++)
    {
      _inner.DoIt();
    }
  }
}
```

## The Magic

To make it all work, the "magic" ✨🦄 [Autofac](https://autofac.org/)
wiring:

```c#
var containerBuilder = new ContainerBuilder();
containerBuilder.RegisterType<Doer>().As<IDoer>();
containerBuilder.RegisterDecorator<LoopingDoer, IDoer>();
```

Probably there is no one in your team who knows
what this does exactly and they'd have to look
at a completely different location (app startup) before it
makes any sense.


# Keep it Simple

After removing the decorator and the Autofac registration,
we ended up with:

```c#
public class Doer : IDoer
{
  public void DoIt()
  {
    for (int i = 0; i < 10; i++)
    {
      // Business Logic here

      // New UseCase example
      if (i == 0)
      {
        // UserStory implementation here
      }
    }
  }
}
```

Which is less code and much easier to
understand, and it also made it easy, trivial even,
to add the required change.


# Decorator Pattern

An excellent design pattern to adhere to the Open/Closed Principle:
Add functionality without modification.

## Intent

It's one of the Structural Patterns from the [GoF](https://en.wikipedia.org/wiki/Design_Patterns).

Attach additional responsibilities to an object dynamically.
Decorators provide a flexible alternative to subclassing for
extending functionality.
{: .notice--info}


## Applicability

When extension by subclassing is impractical because a large number
of independent extensions are possible and would produce an explosion
of subclasses to support every combination.


## Consequences

_More flexible than static inheritance_:  
Responsibilities can be added and removed at run-time whereas inheritance
requires creating a new class for each new combination.

_Avoids feature-laden classes_:  
Instead of trying to support all features in a complex, customizable class,
you define a simple class and add functionality incrementally with Decorator
objects.

_Lots of little objects_:  
The objects differ only in the way they are interconnected. Although easy to
customize, **you can no longer follow the execution flow from the source code
alone and they can be hard to debug**.


## Known Uses

I/O Streams are typically implemented using Decorators and they are a really
good fit for the pattern too!

```c#
byte[] buffer = "test"u8.ToArray();

using (FileStream fileStream = new(path, FileMode.Create, FileAccess.Write))
using (BufferedStream bufferedStream = new(fileStream))
using (GZipStream gzipStream = new(bufferedStream, CompressionMode.Compress))
{
  gzipStream.Write(buffer, 0, buffer.Length);
}
```

We cleanly avoid having to create a `GZipBufferedFileStream` for this particular
case. Having to write all these implementations would result in said explosion
of subclasses and also make it difficult to keep things DRY!


# Conclusion

Using the decorator pattern
instead of writing a simple `foreach` loop;
it's just adding [accidental complexity](https://en.wikipedia.org/wiki/No_Silver_Bullet).

You end up with the consequences of the design pattern (hard to learn and debug)
without any of the benefits.

Keep It Simple.
