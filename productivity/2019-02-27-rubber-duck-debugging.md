---
layout: post
author: Wouter Van Schandevijl
title:  "Rubber Duck Debugging"
date:   2019-02-27 00:00:00
desc: >
  Don't confront someone with your problem right away, Talk to the Duck™ first.
bigimg:
  url: rubber-duck-programming-big.png
  desc: Photo by Mpho Mojapelo
  origin: https://unsplash.com/photos/nJVBdCk6YWc
img:
  url: rubber-duck-programming-duck-slippers.jpg
  desc: "I own one of these! :)"
categories: productivity
tags: [debugging]
interesting:
  - git: atleastimtrying/rubber_duck
    desc: "A tool for one person pairing."
  - url: https://rubberduckdebugging.com
    desc: "Rubber Duck Debugging.com"
toc:
  title: Rubber Ducking
  icon: bug
---

# Talk to the Duck
{: .hide-from-excerpt}

When confronted with a problem, don't ask the person next to you for help right away,
he's probably doing something important as well!

Instead, first tell the duck about it - out loud. If you don't get to a solution,
start explaining your problem in more and more detail until it hits you.
If the mere explanation led to a solution, you avoid unwillingly 
turning your colleague into a [CardboardProgrammer](http://wiki.c2.com/?CardboardProgrammer).

<!--more-->

Most likely the duck will just sit there and appear to have done exactly nothing.
Does it get satisfaction every time it assists someone in finding a solution? We will probably never know.


# More Frivolous

My family somehow turned Rubber Duck Debugging into "give Wouter as many ducks as humanly possible".

The most magnificent duck I've received so far:

{% assign fileName = "rubber-duck-programming-big-duck.jpg" %}
{% assign imgDesc = "At the office. The name is <a href='https://en.wikipedia.org/wiki/The_Story_about_Ping'>Ping</a>, ask her anything!" %}
{% include post/image.html file=fileName alt="Ducky!!" title="Ping hasn't pushed much code though" desc=imgDesc maxWidth="360px" %}


## Rubber Duck Prank

Warning: This stuff is hilarious.

{% include post/youtube.html id="f5d8pVg3Qtg" desc="Not at all relevant to rubber duck debugging but still very funny!" %}


# Further Reading

## Pragmatic Programmers

The original idea was to blog about the Pragmatic Programmer Tips.
The blog turned out quite differently however. Still, here we are, Rubber
Duck Problem Solving, yet another thing I learned from the
Pragmatic Programmers.


**Tip 3: Provide Options, Don't Make Lame Excuses**  
Before you approach anyone to tell them why something can't be done,
is late, or is broken, stop and listen to yourself. **Talk to the rubber
duck** on your monitor, or the cat. Does your excuse sound reasonable,
or stupid? How's it going to sound to your boss? - _Andrew Hunt and David Thomas_
{: .notice--info}

One final tip (in this context!) from them pragmatic programmers: "_Don't be afraid to ask, or to admit that you need help._"
No one knows everything.


## Coding Horror

> The effort of walking an imaginary someone through your problem, step by step and in some detail, 
is what will often lead you to your answer.
... Even if you don't get the answer you wanted, forcing yourself to fully explain your problem will 
frequently lead to new insights and discoveries. - _Jeff Atwood_
in [CodingHorror - Rubber Duck Problem Solving](https://blog.codinghorror.com/rubber-duck-problem-solving/)

StackOverflow also did a [April Fools' Day joke](https://en.wikipedia.org/wiki/Rubber_duck_debugging) in 2018 
about this. Displaying a duck in the corner which, after some apparent thinking & writing, produced a quack.


## WikiWikiWeb

At WikiwikiWeb they also have a [RubberDucking](http://wiki.c2.com/?RubberDucking) page.
They originally called it the [CardboardProgrammer](http://wiki.c2.com/?CardboardProgrammer):

> You've just spent the whole day trying to solve a problem, a design problem, a bug, etc. You know the solution is simple but you just can't see it.
>
> A colleague walks past and notices your anguish: "What's up?".
> "Oh, I'm ...". You explain the problem and what you have done so far. However you suddenly discover the solution before you have finished speaking! So far your colleague has been as useful as a cardboard cutout.

Other, similar techniques:  
- [ReflectiveArticulation](http://wiki.c2.com/?ReflectiveArticulation)
- [ThinkingOutLoud](http://wiki.c2.com/?ThinkingOutLoud)
- [AskTheCode](http://wiki.c2.com/?AskTheCode) aka [UseTheSourceLuke](http://wiki.c2.com/?UseTheSourceLuke)


And to finish this post, a business idea from the RubberDucking wiki page:

> You can even pair program with a rubber duck.  
> It would be perfect if you added a little voice chip that asked, "_Did you write the test?_"
