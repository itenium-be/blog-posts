---
layout: post
author: Simon Verhoeven
title: "Book review: Tidy first?"
subTitle: "Cute, fuzzy little refactorings that nobody could possibly hate on"
date: 2023-12-24
desc: >
  Do we always need to go for full on refactorings, or is there a cute little variant?
  How do we convey the need to do so, and the added value?
bigimg:
  url: tidy-first-big.png
  prompt: "Cute Little Refactorings"
  origin: Midjourney
img:
  url: tidy-first.png
  desc: ""
  title: "Book cover image"
  linkUrl: https://learning.oreilly.com/library/cover/9781098151232/250w
categories: design
tags: [book-review]
toc:
  title: Tidy First!
  icon: book
---

Kent Beck, who certainly isn't an unknown name in the software development world recently published this book, which is based upon his newsletter with the same name.

Price: €39.99 for the eBook  
Publication date: October 2023  
Publisher: O'Reilly Media, Inc.  
Pages: 99  
ISBN: 9781098151249

<!--more-->

In this book, which is the start of a new series we dive into the world of tidyings which are small changes that do not alter the behaviour of our applications.
These are similar to refactorings, but smaller in scope and impact, or as described in the book "cute, fuzzy little refactorings that nobody could possibly hate on".

The "?" in the title is certainly not an error, it's not a how-to book but it offers us premises to makes us think and his insights are shared to help us define a framework. 

## About the book

This book is the start of a new series, and we start with tidying which he calls "the Pringles of software design" to facilitate future (behavioural) changes.

It is split into 3 sections:

* *Tidyings* what are they?
* *Managing* if & when do them?
* *Theory*: why should we do it?


## Tidyings

Here Beck starts us of with some sample tidyings that make behavioural changes (features) in the future easier.  
These small, easily reversible changes that do not need to be preemptively approved, and increase our enjoyment and productivity while coding.

We're walked through 15 examples, some of which may seem quite obvious but it never hurts to think about them, and what they actually mean.

Some examples:
* reading order
* extract helpers
* new interface, old implementation
* ...

## Managing

Here we dive into the more ephemeral question of: should we tidy up, and if so: when?

Like everyone else (hopefully), I like seeing clean code. But as the saying goes "beauty is in the eye of the beholder".  
There is only so much time we can spend on tidying, since after all we also need to focus on the added value.

Ken Beck is not dogmatic in his opinion, and approaches these questions from a trade-off point of view and gives us possible approaches.

He provides us with insights to help us think about:
* when to tidy: before behavior changes, after behavioral changes, later, or never
* when to stop tidying
* whether or not to combine structural changes and behavioral changes
* when and how to batch

And as always align with your team.

## Theory

This was my favourite section. We've diven into the what & when, but what's the why? Which is certainly a question one can expect when tidying.  
Often there can be certain challenges if one cannot properly convey the reasoning for this activity, after all why are you doing this?  
What's the value for the product owner?

And here's where we dive into metaphors from the financial market & option trading to help explain the different offered forms of value:

* optionality
* time/value of money
* coupling & cohesion
* capability of dealing with uncertainty and change
* reversible changes
* ... 

He does a magnificent job of clearly conveying these concepts, which helps us bridge certain gaps. 

# My thoughts

The book contains some very useful insights, and the small bite-sized sections made it an enjoyable, educative read whilst sipping some tea.

While the "what" section might contain a lot of "obvious" things, a refresher never hurts and they are always approached from multiple angles.  
This also makes it an easier recommendation, compared to certain more opiniated books.

The "when" & "why" section are where the real meat was for me.  
In the finalle Ken put a lot of effort into the value (or lack thereof) of doing these changes and gives insights into bridging certain gaps/phrasing towards business-owners to discuss the buy-in.
