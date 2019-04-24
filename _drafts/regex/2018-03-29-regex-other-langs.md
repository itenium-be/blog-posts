---
layout: post
author: Wouter Van Schandevijl
title:  "Regular Expressions: Flavors"
date:   2018-03-29 00:00:00 +0200
desc: >
    Regexes in PowerShell and Perl
categories: javascript
tags: [regex,perl,powershell]
toc:
    title: Regex Flavors
    icon: icon-javascript
---

<!--more-->

# PowerShell




# Perl

```
$input = <STDIN>;
chomp($input);

if ($input =~ m/^([+-]?)([0-9]+)$/i) {
    $sign = $1;
    $value = $2;
} else {
    print "aargh";
}
```


```
$input = <STDIN>;
$input =~ s/match/replace/g;
```
