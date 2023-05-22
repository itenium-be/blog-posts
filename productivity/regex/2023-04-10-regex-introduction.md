---
layout: post
author: Wouter Van Schandevijl
title:  "Regular Expressions: Introduction"
subTitle: "A regex intro so gentle it doesn't even contain a regex"
date:   2023-04-10 00:00:00 +0200
desc: >
  A RegEx introduction: Why in the seven seas would you even want
  to learn this archaic thing.
img:
  url: regex-how-to.png
  desc: It's that easy
bigimg:
  url: regex-big.png
  desc: "Do I really have to?"
  origin: Midjourney
categories: productivity
tags: [regex]
series: regex
interesting:
  - desc: "RexEgg: Not only a cheeky name, but also a good resource"
    url: http://www.rexegg.com
  - desc: "A lot of interesting info, but also a lot of text"
    url: http://www.regular-expressions.info
  - desc: "Regex Cheat Sheet"
    url: https://www.cheatography.com/davechild/cheat-sheets/regular-expressions/
---

You already know regex is a (very) useful skill? Skip to the [Tutorial](https://itenium.be/blog/productivity/regex-tutorial).
If not, allow me to convince you...


Some people, when confronted with a problem, think 'I know, I'll use regular expressions.' Now they have two problems.
**- [Jeffrey Friedl's Blog](http://regex.info/blog/2006-09-15/247)**
{: .notice}


**Or the XKCD version**:

![Perl Problems](/assets/blog-images/regex-xkcd-problems.png "Perl Problems (https://xkcd.com/1171)")


<!--more-->

<br>
<br>
Okay maybe that was not a good start trying to convince you to learn regex.


# The Why

**You don't know you need to know regex until you know regex.**
{: .notice}

## Applicability


Regular Expressions are typically used for user **input validation**.
Another good fit is **extracting, deleting and/or reorganising text**.
Replace a dozen lines of `Substring` and `IndexOf` with one 'pretty' regex!

Other usage in code might be suspect: does the readability really benefit from the regex
or does it just make things more complex.

Be sure to have Unit Tests for all input you'd like to support.  
You'll need them whenever requirements change or bugs need to be fixed.
{: .notice--info}

<!--more-->

<br>
Even with this seemingly limited applicability - if you don't want to shoot yourself in the foot that is -
regexes are still a very useful tool as they can be used in many one time actions.

- Find in (all project) files in your IDE or in a SQL DB
- Quickly find interesting stuff in big files (think logs, data dumps, csvs, etc)
- Convert data that came from A to what B likes
- Many a CLI tool or its parameters understands regex
  - You can even use 'regex' in Excel and Word...

These are the reason this series is posted under the productivity category.

## Example

Whenever I fix a bug that was the result of an error that might have happened in other places aswell,
I might use a regex to find those, even if the other code contains different variable names,
code style etc it would still find them - at least if the regex was written in a way that takes
the context into account.


## Tooling

Don't forget about tooling.
Having **direct feedback on what your regex is matching on your dataset as you type it**.. It's the best 😃  
Any IDE worth its salt has this capability.

Learn to work with **multiple cursors** in your IDE. It is a strong and capable companion to regex:  
Why write the perfect find & replace regex when you can write a simplistic regex on the top of your
head and handle the replacement details with multiple cursor trickery?

You want to avoid falling into the trap of spending more time on your regex than you would spend doing it manually.

![Manual vs Regex Time Trade-Off](/assets/blog-images/regex-time-trade-off.png "Manual vs Regex Time Trade-Off")


## Learning

[RegExOne](https://regexone.com): An interactive tutorial!


Not necessarily learning resources, but still pretty handy:
interactive RegEx testers. Paste a RegEx you found and
match it against your input. Plus they explain the regex visually.

- [RegExr](https://regexr.com/)
- [RegEx101](https://regex101.com/)
- [debuggex](https://www.debuggex.com/?flavor=javascript)


**Mastering Regular Expressions**

Even though the name of the book is [Mastering Regular Expressions](https://www.amazon.com/Mastering-Regular-Expressions-Jeffrey-Friedl/dp/0596528124)
it still starts really slow and is perfectly readable by someone with no (or very little) regex experience.



# The Why Not

Always be wary before resorting to regex 😉

## Implementations

As if RegExes are not confusing enough in and by themselves, there are different implementations of regex.
While all implementations are largely the same, you may still find yourself in a situation where the regex
works perfectly in your IDE but not from your application.

Some examples (or [a full list](https://gist.github.com/CMCDragonkai/6c933f4a7d713ef712145c5eb94a1816)):
- Features not implemented. ex: named capture groups in JS (since added in [ECMAScript 2018](https://github.com/tc39/proposal-regexp-named-groups))
- `\b` vs `<|>` for example in Visual Studio
- Different behavior of 'small' syntax errors. ex: throwing vs silently continueing
- .NET has `(?#comment)`, Java has not. Java has `\Q...\E`, .NET has not


<!--
Naah, not funny enough
![Basically too many differences](/assets/blog-images/regex-hate.jpg "Basically too many differences")
-->


## Inapplicability

### Html

There are the obvious ones: throwing [regex against HTML](https://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags/1732454#1732454) or XML.


![HTML Parsing](/assets/blog-images/regex-html.png "HTML Parsing")

**Use a parser instead**. And then you can throw some jquery against it or whatever API your language exposes.
It's going to be a lot more readable, maintainable and more robust to changes in the HTML.

I might get hate mail for posting this on the interwebs (or worse) but really: if you have
**some HTML or XML** which you know always has **the exact same structure** and you need
to **do something simple**, well... Why not? What's the worst that can happen by using a regex to extract
`<SomeUniqueNode>SOME ID WITH KNOWN STRUCTURE</SomeUniqueNode>` and save you importing a third
party library which is probably bloated and inefficiently written?

### Everywhere

I once worked with a senior developer who, I think, had just discovered regex.
And, he had become so fond of them (or something?) that all code he touched was littered with little regexes after.
I mean, why keep `str.split('|')[0]` around when you can replace it with a regex, right.

To top it off, all his regexes were needlessly long, most of them could be written in
1/3 or even in 1/2 of his "style".

Which is how we fluently arrive at...


## Writing vs Reading


![Writing vs Reading RegEx](/assets/blog-images/regex-writing-vs-reading.png "Writing vs Reading RegEx")

It's funny because it's true. Requirements change and well, good luck deciphering your own regex.  
Or heaven forbids, one someone else wrote! 😱


# The **REAL** Why

Let's be honest here...

![Perl Problems](/assets/blog-images/regex-xkcd-fantasy.png "Perl Problems (https://xkcd.com/208/)")

**Moving on**

Did I already mention that you'll want to have tests of the code using regexes, especially if they tend
to change often? Don't be shy to write them, even if they are the only ones in your repository.
