---
layout: post
author: Wouter Van Schandevijl
title:  "Less for your git pager"
date:   2018-08-19 00:00:00 +0200
img: less.png
desc: >
    How to navigate git diffs, logs, ...
    on the CLI with less
categories: productivity
tags: [git]
toc:
    title: Less Pager
    icon: icon-git
---


Your current configuration:  
```
git config --get core.pager
```

If this returns nothing, there is room for improvement there!

This post details the parameters that can be passed to `less`
and also how to navigate once less is running. Things not relevant
for git are omitted.

**TL&DR**:  

Configure something sensible:  
```sh
# Short version
git config --global core.pager "less -eFiJM~ -j3 --tabs=3"

# Not so short version
git config --global core.pager "less --LONG-PROMPT --tabs=3 --quit-at-eof --quit-if-one-screen --tilde --jump-target=3 --ignore-case --status-column"
```

{% include kbd k="h" l="See all less commands." %}

<!--more-->

Add a link to the [man page][less-man-page] and this topic is hereby extensively covered :)

# Parameters for core.pager

Git defaults to `less FRSX`.

## Display

Default cursor is just a `:`
{% include kbd k="-m, --long-prompt" l="Show # bytes passed" %}
{% include kbd k="-M, --LONG-PROMPT" l="Show current lines displayed (ex: 1-57)" %}

<br>
{% include kbd k="-N, --LINE-NUMBERS" l="Display line numbers" %}
{% include kbd k="-s, --squeeze-blank-lines" l="Fold multiple empty lines to just one (doesn't fold empty + or - lines though)" %}
{% include kbd k="-S, --chop-long-lines" l="Truncate long lines instead of wrapping" %}
{% include kbd k="-x, --window=x" l="Set default window line size to x for PgUp/PgDown" %}


## Searching

{% include kbd k="-I, --IGNORE-CASE" l="Case insensitive search" %}
{% include kbd k="-i, --ignore-case" l="Case insensitive search unless uppercase characters are found in the search pattern" %}
{% include kbd k="-jX, --jump-target=X" l="with X a number: Show search result X lines from the top of the screen" %}
{% include kbd k="-J, --status-column" l="Place * on lines with search results" %}

## Quitting

{% include kbd k="-e, --quit-at-eof" l="Exits less when reached end twice" %}
{% include kbd k="-E, --QUIT-AT-EOF" l="Exits less when first reached end" %}
{% include kbd k="-F, --quit-if-one-screen" l="Exits when output is less than 1 page" %}
{% include kbd k="-~, --tilde" l="Lines after end of file are blank, not ~" %}


# Moving around

Syntax used:  
- `xp`: Triggers for any number x followed by `p`.
- `xp`: Also triggers when just pressing `p`.
- Omitted: Many keys can be combined with `Control` for the same effect.

## Going down

{% include kbd k="xe, xj, xENTER" l="Forward one line or x lines if x is provided" %}

{% include kbd k="xd" l="Forward half a page. If x is specified it becomes the new default for d" %}

{% include kbd k="xPgDwn, xSPACE, xf" l="Forward a page. x is optional, if provided it sets the size of the page (once)" %}
{% include kbd k="xz" l="Sets the size of x as the page size (in lines) for all subsequent calls of f, SPACE and PgDwn" %}

{% include kbd k="G, >" l="Forward to the end" %}


## Going up

{% include kbd k="xy, xk, CONTROL+P" l="Backward one line or x lines if x is provided" %}

{% include kbd k="xu" l="Backward half a page. If x is specified it becomes the new default for u" %}

{% include kbd k="xPgUp, xb, ESC+V" l="Backward a page. x is optional, if provided it sets the size of the page (once)" %}
{% include kbd k="xw" l="Sets the size of x as the page size (in lines) for all subsequent calls of b, PgUp" %}

{% include kbd k="g, <" l="Backward to the beginning" %}


## Searching

See searching parameters above for configuring case sensitivity.

{% include kbd k="/" l="Search down for text or regex" %}
{% include kbd k="?" l="Search up" %}
{% include kbd k="n" l="Goto next match" %}
{% include kbd k="N" l="Goto previous match" %}
{% include kbd k="&" l="Seach and show only the matching lines" %}

## Other

{% include kbd k="s + filename" l="Save to file and inspect with your weapon of choice" %}
{% include kbd k="(, ), {, }, [, ]" l="When the character presed is on top of the page, scroll to its counterpart" %}
{% include kbd k="xp, x%" l="Go to x% position in document" %}

<br>
**Marking**:  
{% include kbd k="mx" l="With x any character. Mark this position as x." %}
{% include kbd k="'x" l="Goto the position marked as x" %}


# Quitting

{% include kbd k="ESC, SPACE" l="ESC followed by SPACE: continue scrolling after end of stream" %}
{% include kbd k="q, Q, :q, :Q, ZZ" l="Exit less" %}
{% include kbd k="CONTROL+l" l="Clear screen afterwards?" %}


# Summary

After a few years of just using `PgUp` and `PgDown` I guess this research
was way overdue.

[less-man-page]: https://linux.die.net/man/1/less
