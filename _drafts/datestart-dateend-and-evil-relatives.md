---
layout: post
title:  "DateStart & DateEnd (And Their Evil Relatives)"
date:   2017-04-12 15:00:00 +0200
categories: object-oriented
tags: [design patterns]
---


<!--more-->

or one of their equally evil brothers: “DateStart & TimeSpan” or “Day/Month only in DateTime”, etc.

> This is the case for the ValueObject

--> Especially for ValueTypes found in the Business!
--> StartDate, StartBy, ExecutedDate, ExecutedBy, ReviewedDate, ReviewedBy: class Task
---> func Start(id by) { StartBy = id; StartDate = DateTime.Now }
--> And yes, you guessed it, Date+By is a perfect candidate for a ValueObject too!
--> (If these Date/By combos keep growing, a different model could be better there)

Advantages:
--> Keep intellisense small
--> Keep things that belong together - together.

DateStart, StartBy: Might read better but inconsistent etc...


Not creating a value object for coherent pairs of value types is a missed opportunity.

You end up creating all sorts of helper classes for often sought after functionality, which might even be duplicated as they are being implemented by different programmers.

- Expressiveness

What we need in case of DateStart and DateEnd variables: IsIn(DateTime);


TellDontAsk
-----------



+ Discoverability
+ Same/Similar private helper methods scattered around


Example for EntityFramework (c#) and Hibernate (Java)?
