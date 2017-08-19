---
layout: post
title:  ".NET Number and Date Formatting"
date:   2017-04-20 15:00:00 +0200
categories: dotnet
tags: [net,date,globalization,cheat-sheet]
---

{% include toc title="Formatting" icon="dot-circle-o" %}

A cheat sheet with everything there is to know about 
formatting the primitive types, `DateTime`s and `TimeSpan`s in .NET.

<!--more-->


Stringify
=========

The different ways of formatting these ValueTypes.
```c#
// ValueType.ToString("format");
Console.Write(5000.ToString("#,##0"));

// Composite Formatting
Console.WriteLine("{0:yyyy-MM-dd HH:mm:ss}", DateTime.Now);

var str = new StringBuilder();
str.AppendFormat("{0:F} - {1:0.00}", 1, 2);

using (var stream = new StreamWriter(@"c:\temp\aargh.txt"))
{
	stream.Write("{0} -> {1}", 1, 2);
}

// String interpolation
int i = 5;
Debug.WriteLine($"{i:F}");
{% comment %}
    The {{0}} looks weird because it needs to escape the Liquid template engine
    http://stackoverflow.com/questions/3426182/how-to-escape-liquid-template-tags
{% endcomment %}
// Escaping {}
Debug.WriteLine("{{"{{0"}}}} = {0}", 5); // {0} = 5
// Nested braces like {{"{{0:D"}}}} are NOT supported

// Optional alignment component
// Positive: Right-align
// Negative: Left-align
var table = new StringBuilder();
table.AppendFormat("{0,-10} | {1,5}\n", "Name", "Age");
table.AppendFormat("{0,-10} | {1,5:F}\n", "Bob", 18);
table.AppendFormat("{0,-10} | {1,5:F}\n", "Alice", 24);
table.AppendFormat("{0,-10} | {1,5:F}\n", "Methuselah", 969);

// table.ToString():
Name       |   Age
Bob        | 18.00
Alice      | 24.00
Methuselah | 969.00
```


* * *



Cultures
========

The culture for all threads defaults to the Windows system culture.

```c#
using System.Threading;
using System.Globalization;

// Number, Date, ... formatting
Thread.CurrentThread.CurrentCulture = new CultureInfo("en-US");

// Translations from ResX files
Thread.CurrentThread.CurrentUICulture = new CultureInfo("en-US");
```

Starting from .NET Framework 4.5, default cultures can be set for all new and all existing
threads in the AppDomain that have not explicitely set their `CurrentThread.CurrentCulture`.

```c#
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("en-US");
CultureInfo.DefaultThreadCurrentUICulture = new CultureInfo("en-US");
```


* * *


Number Formatting
=================
### Standard Numeric Formats

```c#
decimal m = 1000.5050M;
string pretty = money.ToString(SomeFormat);
```

| SomeFormat | Name         | Result en-US | Result nl-BE |
|------------|--------------|--------------|--------------|
| C          | Currency     | $1,000.51    | € 1.000,51   |
| C0         |              | $1,001       | € 1.001      |
| C3         |              | $1,000.505   | € 1.000,505  |
| P          | Percent      | 100,050.50 % | 100 050,50%  |
| 0.5 ("P1") |              | 50.0 %       | 50,0%        |
| N          | Number       | 1,000.51     | 1 000,51     |
{: .table-code}

`SomeFormat` is case insensitive. All standard numeric formats
may be suffixed with an optional precision specifier. If no
number is provided, it defaults to the value of `CurrencyDecimalDigits`
in the configuration of the current culture `NumberFormatInfo`.
```c#
Thread.CurrentThread.CurrentCulture.NumberFormat
```

Other standard numeric formats:
```c#
// Pad integral types (int, byte, long, ...)
15.ToString("D4"); // 0015

// Fixed point
15.495.ToString("F"); // 15.50

// Round-trip (Single, Double, BigInteger)
// ATTN: /platform:x64 vs /platform:anycpu (Use "G17" instead?)
(15.0123453f).ToString("N7"); // 15.0123500
(15.0123453f).ToString("R0"); // 15.0123453

// "E" => Exponential notation
// "G" => General, the more compact of either "F" or "E" (default)
// "X" => Hexidecimal (integral only)
```

### Custom formats:

| Specifier | Description                                        |
|-----------|----------------------------------------------------|
| 0         | Zero placeholder (force display)                   |
| #         | Digit placeholder (do not display non-significant) |
| .         | Decimal point                                      |
| ,         | Thousands separator                                |
| \         | Escape next character                              |
| 'string'  | Escape entire string                               |
{: .table-margin}

```c#
// Explicitly show the +/- sign
10.ToString("positive;negative;zero"); // positive
10.ToString("+#;-#;0"); // +10
0.ToString("+0;-0"); // +0

// Percentage (NumberFormatInfo.PercentSymbol)
0.01.ToString("#%"); // 1%

// ‰ == "\u2030" (Per mille)
0.01.ToString("#\u2030"); // 10‰

// , is also used as number scaling specifier
// Where each , divides the number by 1000
123456789.ToString("#,,"); // 123

// Bananas (https://www.youtube.com/watch?v=EdR72ACz_xI)
1234586789.ToString(@"\##,##0.00\#"); // #1,234,586,789.00#
1234586789.ToString(@"\##,##0,,.00\#"); // #1,234.59#
8009999999.ToString("000-000-0000"); // 800-999-9999
```


* * *


Date Formatting
===============
### Standard Date and Time Format Strings

Configuration: `System.Globalization.DateTimeFormatInfo`

```c#
var dt = new DateTime(2017, 1, 31, 22, 15, 59);
```

| Format | Name                 | Result en-US                                | Result nl-BE                     |
|--------|----------------------|---------------------------------------------|----------------------------------|
| d      | Short date           | 1/31/2017                                   | 31/01/2017                       |
| D      | Long date            | Tuesday, January 31, 2017                   | dinsdag 31 januari 2017          |
| f      | Full (short time)    | Tuesday, January 31, 2017 10:15 PM          | dinsdag 31 januari 2017 22:15    |
| F      | Full (long time)     | Tuesday, January 31, 2017 10:15:59 PM       | dinsdag 31 januari 2017 22:15:59 |
| U      | Universal full       | Like "F" but converted to UTC               |                                  |
| g      | General (short time) | 1/31/2017 10:15 PM                          | 31/01/2017 22:15                 |
| G      | General (long time)  | 1/31/2017 10:15:59 PM                       | 31/01/2017 22:15:59              |
| 
| Partials
| M / m  | Month/day            | January 31                                  | 31 januari
| Y / y  | Year/month           | January 2017                                | januari 2017
| t      | Short time           | 10:15 PM                                    | 22:15
| T      | Long time            | 10:15:59 PM                                 | 22:15:59
{: .table-code}

**Culture insensitive formats**

```c#
var dt = new DateTime(2017, 1, 31, 22, 15, 59);
var dto = new DateTimeOffset(dt, TimeSpan.FromHours(2));
```

| Format | Name                 | dt.ToString("format")                | dto.ToString("format")                  |
|--------|----------------------|--------------------------------------|-----------------------------------------|
| s      | Sortable             | 2017-01-31T22:15:59                  | 2017-01-31T22:15:59
| O / o  | Round-trip           | 2017-01-31T22:15:59.0000000          | 2017-01-31T22:15:59.0000000+02:00
| u      | Universal sortable   | 2017-01-31 22:15:59Z                 | 2017-01-31 20:15:59Z
| R / r  | RFC1123              | Tue, 31 Jan 2017 22:15:59 GMT        | Tue, 31 Jan 2017 20:15:59 GMT
{: .table-code}

### Custom Date and Time Format Strings

| Specifier | Description             |
|-----------|-------------------------|
| Date
| d         | Day of month (1 -> 31)
| dd        | 01 -> 31
| ddd       | Mon -> Sun
| dddd      | Monday -> Sunday
| M         | Month of year (1 -> 12)
| MM        | 01 -> 12
| MMM       | Jan -> Dec
| MMMM      | January -> December
| y         | Year (0 -> 99)
| yy        | 00 -> 99
| yyyy      | 0001 -> 2017
| 
| Time
| h         | Hour (1 -> 12)
| HH        | 01 -> 24
| m         | Minute (1 -> 59)
| mm        | 01 -> 59
| ss        | Second (01 -> 59)
| 
| Special
| :         | Time separator
| tt        | AM/PM
| .         | Fractional seconds separator
| /         | Date separator
| 'literal' | String literal
| "literal" | String literal
| \         | Escape character
{: .table-margin}


```c#
// Use % for single character custom formats
DateTime.Now.ToString("%M");

// Other:
// g -> A.D
// K -> Time zone info: Kind Unspecified, Kind Utc, Kind Local or -01:00
// fff -> Milliseconds (Other from f to FFFFFFF)
// z -> Hours offset from Utc
```


TimeSpan Formatting
-------------------

```c#
var span = new TimeSpan(31, 22, 15, 59);
```

| Specifier | Format                          | Result                  |
|-----------|---------------------------------|-------------------------|
| c         | [d'.']hh':'mm':'ss['.'fffffff]  | 31.22:15:59
| g         | [-][d':']h':'mm':'ss[.FFFFFFF]  | 31:22:15:59
| G         | [-]d':'hh':'mm':'ss.fffffff     | 31:22:15:59.0000000
{: .table-margin}

The square brackets will omit the part if not significant. They are not valid as custom format.

**Custom formats**:  
Like Date Custom formats but each part represents the whole part in the time interval.
```c#
span.ToString(@"dd\.hh\:mm\:ss"); // 31.22:15:59
Console.WriteLine("{0:%d}", span); // 31
Console.WriteLine("{0:ddddd}", span); // 00031
```
