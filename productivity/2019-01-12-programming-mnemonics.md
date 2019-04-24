---
layout: post
author: Wouter Van Schandevijl
title:  "Programming Mnemonics"
date:   2019-01-12 00:00:00
desc: >
  Mnemonics: aiding or designed to aid the memory. For Developers. Yeah.
img:
  url: mnemonics-xkcd.png
  desc: "XKCD Presents: Some New Science Mnemonics"
  origin: https://xkcd.com/992/
interesting:
  - desc: "XKCD Presents: Some New Science Mnemonics"
    url: "https://xkcd.com/992/"
categories: productivity
tags: [cheat-sheet]
toc:
  title: Mnemonics
  icon: fa-heartbeat
---

Mnemonics - helping developers remember intrinsics since 1973.

> A mnemonic  
> a system such as a pattern of letters, ideas, or associations which assists in remembering something.

<!--more-->

One I couldn't remember for the longest time until I found this gem:

`\r\n`: **R**etur**N** - the R is before the N

And a quick reminder:  
```
OS & Windows: \r\n 0D0A (hex), 13,10 (decimal)
Unix & Mac OS X: LF, \n, 0A, 10, Newline
Macintosh (OS 9): CR, \r, 0D, 13
```

**Programming**:  
- Parameters vs Arguments: **A**rguments are **A**ctual

- `\`: Backslash is a man falling backwards.
- `/`: Forwardslash is the opposite.

- `.unshift`: Makes it bigger. Longer word than .shift
- `.shift`: Makes it smaller. Shorter word than .unshift


**JavaScript:**  
- `.call`: Pass arguments as **c**olumns - `fn.call(this, arg1, arg2, ...)`
- `.apply` Pass arguments as an **a**rray - `fn.call(this, [arg1, arg2, ...])`


**Markdown:**  
- "squared circle" aka `[]()` aka `[word](http://link)`


**Regex flags:**  
- `/s`: Allows `.*` to **Slurp** up numerous lines
- `/m`: Allows `^` and `$` to match in the **Middle** of the string




# CSS

Order for margins/paddings

> If you don't remember there will be **TRouBLe**  
> margin: T R B L;
> <small>or: The Really Big Lebowski</small>
> <small>or a clock: 12h 3h 6h 9h</small>


 Pseudo class order

> **L**o**V**e / **HA**te  
> `a:link`, `a:visited`, `a:hover`, `a:active`



.class vs #id

> Hash is not a class


# Mathy

The order of math operations:

> Please Excuse My Dear Aunt Sally  
> (Parentheses, Exponents, Multiply, Divide, Add, and Subtract)


To remember the value of pi: 

> May I have a large container of coffee?  
> (The number of letters in each word equals the numerals in pi = 3.1415927)



# Even for tar

Even for tar one exists, as the following IRC conversation illustrates:

```
<sdmkun> tar -xzf merc.tgz what the fuck
<sdmkun> how the fuck do you people remember this shit
<bucketmouse> just think with a german accent
<bucketmouse> XTRACT ZE FILES
```
