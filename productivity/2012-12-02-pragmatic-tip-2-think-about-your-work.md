---
layout: post
author: Wouter Van Schandevijl
title:  "Pragmatic Tip 2: Think! About Your Work"
date:   2012-12-02
desc: >
  The second blog post of the original blog.
  "THINK! About Your Work"
img:
  url: pragmatic-programmer-think.jpg
  desc: "The old IBM corporate motto 'THINK!'"
categories: productivity
tags: [pragmatic-tips]
series: pragmatic-tips
toc:
  title: THINK!
  icon: wordpress
---

> "_Turn off the autopilot and take control. Constantly critique and appraise your work._"  
> — The Pragmatic Programmers


<!--more-->

Dave and Thomas mantra is the old IBM corporate motto "THINK!":
While working, do not ever stop questioning what you are doing.
_Think!_ about opportunities to improve the way you work.
Any manual task is prone to error and often recurring tasks can probably be automated.
Find ways to refine your skills and always be on the lookout for new adventures.


One such adventure to make you stronger is learning about Design Patterns.
And a great way to learn more about patterns is by [teaching about them](https://github.com/itenium-be/Talk-DesignPatterns).

# Learning Design Patterns

How do you convince a developer unfamiliar with Design Patterns to pick up a book on the topic. There are a few amazing books about
Design Patterns out there of which [Design Patterns: Elements of Reusable Object-Oriented Software](http://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=pd_bxgy_b_img_y)
is the one most often recommended. 
Now, there are many (semi-)technical books out there that are not only educational but fun to read also. That one, however interesting,
is not one of them. So outright advising to pick it up might not be a good idea, certainly not if this developer hasn't read much before.
Starting with a book about perhaps at first seemingly irrelevant or rarely applicable concepts might quickly result in a "BOOORING" feeling
(and resulting in the book being thrown in some corner to gather dust.) Try convincing this person afterwards about another "amazing" IT book!


It is for this reason that I always have some reserve about recommending Design Patterns. It might be better to warm them up to the idea
and usefulness of patterns first and, if you manage to pique their interest, then tell them about the Gang of Four patterns book.  
You could start with saying that patterns are actually more a means of communicating in the team. Adding the name of the pattern to the name 
of the type for example facilitates communication between maintainers. Any time you use the name of such proven pattern, the intent, motivation
and consequences of using it are all communicated with just one word.


If the developer has had some design experience, you could continue with that no doubt she will already know and have used some of the patterns
described in the book. Then is the moment to present a few of the more common patterns with the aim to get that "Aha!" reaction when a certain pattern
sinks in. Begin perhaps with explaining (arguably) the easiest pattern of them all: [Template Method](http://en.wikipedia.org/wiki/Template_method_pattern).
Or if the developer is sometimes falling in the
procedural trap of excessively repeated if/else, typeof or switch statements (lists that seem to grow with every other new requirement), you might want
to explain how [Strategy](http://en.wikipedia.org/wiki/Strategy_pattern) (a.k.a. Policy) could solve these
[Open/Closed Principle](http://en.wikipedia.org/wiki/Open/closed_principle) violations instead.


And finally we are back where we started: "Think! About Your Work"

When learning about patterns, one must be careful to think of them as a new tool available to use when required, not as the new way of doing things
– as is in fact the case with all new things learned. A well designed system can incorporate many patterns used together but we must always be careful
(_Think!_) when applying a pattern or risk overusing them, applying them without fully understanding the consequences or we might even unconsciously
start molding problems to fit a pattern (Just to be able to implement it now?:). Probably even more important than the patterns themselves, the risks
should always be clearly communicated when introducing someone to design patterns.

# There are no absolute truths

Another way to approach "selling" the GoF Design Patterns book to someone would be by not sampling the patterns themselves but by illustrating
that the patterns are often implementations of two underlying principles of object-oriented design (described in much detail in section 1.6 of their
book "How Design Patterns Solve Design Problems".) These principles are "**Program to an interface, not an implementation**" and "**Favor object composition
over class inheritance**". I never quite understood why these principles are so hidden in the book (I once searched for like 20 minutes for them) instead
of in some big fat font as I believe that if someone were to just remember two things from the book, it should be those principles.


What happens when someone sees "Program to an interface, not an implementation" and takes it as an absolute truth is that systems get designed where
half of the Types are Interfaces and the other half are (in most cases) their only implementation. Due that one sentence, the next project I inherited had
its Types almost doubled.


Pretty please _Think!_, if there is only one implementation required and there is no apparent reason for a second one to be ever needed, do not define
an interface for the sake of defining an interface. Unnecessary types clutter the project with unneeded complexity. Everything in a system should have
a purpose. If you have only interfaces that are actually implemented multiple times then when you see an interface, it is practically shouting at you
"hey look at me, interesting stuff is happening here!". The code does not communicate this when there are interfaces for normal and special cases alike.
Besides, the simpler a project remains, the better. It'll get plenty of complexity anyhow, there is no reason to be adding some Types already for
functionality that is not (yet) required.


If you have knowledge of the domain and/or know the product owner well, you might be able to predict the ways the design needs to be flexible but
unless you are absolutely certain it will be required do not add it. I'd like to already refer to Pragmatic Tip 51: "**Don’t Gather Requirements–Dig
for Them**", if you _Think!_ flexibility might be required, try to verify this with a domain expert before implementation. As you get to know the domain
and the product owner(s) better, you'll be able to make better and better judgments regarding what the next feature requests will be.


Of course, it is possible a second implementation does become required where no interface was created. Change is inevitable and some refactoring is
going to be necessary. But when was the last time you added an interface or feature "just in case" that actually ended up being required and could
be used AS IS? Chances are slim that an API will be able to handle all requirements (they were unknown!) without refactoring. Or it can preserve the
signature but introduce a hack. Certainly at the beginning of a project, when the specs are not exactly frozen (or even not yet existing), all object
structure and relations are constantly in movement. And in the end, even things that were never ever – ever – going to change end up changing quite a
lot (and back and forth). Dave & Thomas have the remedy for any headache this "flexibility" might incur to you at your work place: 
Pragmatic Tip 14: "**There Are No Final Decisions**" . This also works the other way. If you provided hooks for extensibility and they turn out not to be
required, the unneeded complexity can always be taken back out. No regrets. Plus I like to delete dead code.

Next up is "Provide Options, Don’t Make Lame Excuses", one of my favorite tips!
