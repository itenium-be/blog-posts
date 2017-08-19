---
layout: post
title:  ".NET Parsing and Formatting"
date:   2017-04-20 15:00:00 +0200
categories: dotnet
tags: [net]
---

<!--more-->

Separate post: DateTime formatting: .NET vs MomentJS?


Enums
=====
https://msdn.microsoft.com/en-us/library/c3s1ez6e.aspx
DayOfWeek MyDays = (DayOfWeek)Enum.Parse(typeof(DayOfWeek), "Thursday");


Guid
====
https://msdn.microsoft.com/en-us/library/97af8hh4(v=vs.110).aspx
http://stackoverflow.com/questions/7513391/newguid-vs-system-guid-newguid-tostringd
```c#
Guid.ToString("N")
```


IFormatProvider
===============
https://msdn.microsoft.com/en-us/library/system.icustomformatter.aspx
IFormattable, IFormatProvider, ICustomFormatter

Regex
=====

Parsing
=======

# https://msdn.microsoft.com/en-us/library/xbtzcc4w.aspx


```c#
double.Parse("dbl");

DateTime.Parse("date");
DateTime.ParseExact("date", "format", CultureInfo.InvariantCulture);
DateTime.TryParseExact("date", "format", CultureInfo.InvariantCulture, out DateTime dt);
```
