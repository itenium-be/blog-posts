---
layout: post
author: Wouter Van Schandevijl
title:  ".NET Regex Tutorial"
date:   2019-04-19 01:00:00
desc: >
  Basic .NET Regex cheat sheet
bigimg:
  url: regex-water-big.png
  desc: Unsplash picture by Àlex Folguera
  origin: https://unsplash.com/photos/eSfsjp6xuYU
img: regex-matches.jpg
imgdesc: Picture by Devin Avery
imgurl: https://unsplash.com/photos/ZsgPd6ovNag
categories: dotnet
tags: [cheat-sheet,regex]
interesting:
  - url: http://blog.stevenlevithan.com/archives/balancing-groups
    desc: "Steven Levithan: Fun with .NET Regex Balancing Groups"
toc:
  title: 🔥 .NET Regex
---

Not nearly as confusing as it is in JavaScript.

```c#
using System.Text.RegularExpressions;

bool mach = Regex.IsMatch("input", @"\w+", RegexOptions.None);
Match match = Regex.Match("input", @"\w+");
IReadOnlyList<Match> matches = Regex.Matches("input", @"\w+");
string result = Regex.Replace("input", @"(\w+)", "$1");
```


<!--more-->

When executing the same regex many times, prefer the static methods on `Regex`
over `new Regex()` as they keep an internal cache for performance.

Its methods may throw a [`RegexMatchTimeoutException`](https://docs.microsoft.com/en-us/dotnet/api/system.text.regularexpressions.regexmatchtimeoutexception?)



# Creation

```csharp
var regex = new Regex(@"\d+", RegexOptions.None);

var commentedRegex = new Regex(@"
    \d    # Any digit
    [0-9] # With character class
    ", RegexOptions.IgnorePatternWhitespace);
```

- `RegexOptions.Compiled`: Increased startup time for decreased execution time.
- `RegexOptions.IgnoreCase`.
- `RegexOptions.ExplicitCapture`: Only explicitly named/numbered groups are captured. (Skip the `(?:…)`).
- `RegexOptions.Multiline`: `^` and `$` match beginning of line.
- `RegexOptions.Singleline`: `.` matches `\n`.


# Tes A Match

```csharp
Match match = Regex.Match("1-22-333", @"(\d+)-(\d+)-(\d+)(-\d+)?");

IReadOnlyList<Group> groups = match.Groups;
Assert.Equal(5, groups.Count);

Assert.Equal("1-22-333", groups.First().Value);

Group one = match.Groups.Skip(1).First();
Assert.Equal("1", one.Value);

Group fours = match.Groups.Last();
Assert.False(fours.Success);
```

## Multiple Named Matches

```csharp
string pattern = @"(?<one>\d+)-(?<two>\d+)";
IReadOnlyList<Match> matches = Regex.Matches("1-22, 1-2", pattern);

Assert.Equal(2, matches.Count);

Match first = matches.First();
Assert.Equal("1-22", first.Value);

Match second = matches.Last();
Assert.Equal("1-2", second.Value);


IReadOnlyList<Group> groups = first.Groups;
Group one = groups.Skip(1).First();
Assert.Equal("one", one.Name);
Assert.Equal("1", one.Value);
```


# Replace

#### Basic Usage
```c#
var regex = new Regex(@"(\d)-(\d+)");
var result = regex.Replace("1-22", "$1+$2");
Assert.Equal("1+22", result);

result = Regex.Replace("1", @"(?<amount>\d+)", "$$ ${amount}");
Assert.Equal("$ 1", result);
```

#### Replace with Function
```c#
string input = "Hello World!";
string pattern = @"Hello";

var result = Regex.Replace(input, pattern, (Match m) => $"<b>{m.Value}</b>");
Assert.Equal("<b>Hello</b> World!", result);
```

#### Other Substitutions
- `$&` : entire match
- ```$```` : before
- `$'` : after
- `$+` : last captured group
- `$_` : entire input string
