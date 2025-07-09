---
layout: post
author: Wouter Van Schandevijl
title:  "Regular Expressions: Tutorial"
subTitle: "The absolute minimum amount of regex to learn"
date:   2023-05-22 00:00:00 +0200
desc: >
  Regex tutorial: after our gentle regex intro, we now dive
  into the minimum set of regex you should really know.
img:
  url: regex-be-normal.jpg
  desc: "A small investment in regex now. Loads of return in the future..."
bigimg:
  url: regex-big.png
  origin: Midjourney
categories: productivity
interesting:
  - desc: "Powerpoint pptx and hands-on exercises"
    git: itenium-be/Regex-Tutorial
tags: [regex,tutorial,cheat-sheet]
series: regex
toc:
  title: Tutorial
---

The minimum Regex one should know and still be fairly productive.


<!--more-->

Almost all of this stuff should work in most regex implementations.  
Notable exceptions are: `\<` and `\>`, `[a-Z]`, `\Q\E`. Your mileage may vary.


# Basic Syntax


| Regex      | Matches                                                 | Remarks
|------------|---------------------------------------------------------|--------
| **Literals**
| abc        | Literals match themselves. Here: `abc`
|
| **Metacharacters**
| ^s         | `s` but only at the start of the input
| e$         | `e` but only at the end.                                | `^` and `$` match a position.
| \\$        | Escape character `\`: Match any `$`
| .          | Match any one character                                 | Matches `\r\n` with dotall (s) flag
| \\<e       | Word that starts with `e`                               | Word boundaries: `[a-zA-Z0-9_]`
| e\\>       | That ends with `e`
| \b         | Word boundary                                           | Between `\w` and `\W`
| 
| **Character Classes**
| [az]       | `a` or `z`
| [0-9]      | A single digit                                          | `\d`
| [a-zA-Z]   |
| [a-Z]      | This **might** also include `[]\\^_` and a literal `
| [\n$^-]    | A newline, `$`, `^` or a hyphen (`-`)                   | A `^` at the start negates the class
| [^a\b]     | Everything but `a` and 'backspace'                      | `[\b]` (backspace) vs `\b` (word boundary)
|
| **Quantifiers**
| a?          | Zero or one
| a*          | Zero or more
| a+          | One or more
| a{2,5}      | Two to five
| a{4}        | Exactly four
| a{5,}       | Five or more                                            | `a{,5}` for 0 to 5
|
| **Grouping**
| (ab\|yz)   | Literal `ab` or `yz`.                                    | Alternation
| (?:ab)     | `ab` but non-capturing group
| (\w+) \1   | `\1` matches the same thing as `(\w+)`                   | Backreferences (also `\2` etc)
{: .table-code}


## Shorthands

| Shorthand | Meaning          | Remarks
|-----------|------------------|--------
| \d        | [0-9]            | `\D` -> `[^0-9]`
| \w        | [a-zA-Z0-9_]
| \s        | Whitespace
| \t        | Tab
| \r        | Carriage return
| \n        | Newline
| \b        | Word boundary    | `\A` and `\Z`: Start/End of string
| \Q...\E   | Literal sequence | Ex: `\Qlite[]ral\E`
{: .table-code}

Shorthands can be inverted by capitalizing them: `\D` (not a digit)

Also `\v` (vertical tab), `\f` (form feed).  
Inside a character class `\b` matches backspace.

# Modifiers

| Modifier     | Description
|--------------|------------
| `g`(lobal)     | JS: Match more than once
| `i`(nsensitive)| Case insensitive matching
| `m`(ultiline)  | `^$` match every line in the string (vs `\A` and `\Z`)
| `s` (dotall)   | `.` matches `\r|\n`
{: .table-code}


# Replacement

| Replacement | Description
|-------------|------------
| $1          | First captured group
| $2          | Second captured group
| $$          | A literal `$`
| $&          | Entire match
| $`          | Before matched string
| $'          | After matched string
| $+          | Last matched string
{: .table-code}

Some implementations use `\` instead of `$`.

Use non-capturing groups `(?:)` to keep your backreferences (`$1`, `$2`, ...) in check.  
Or use named groups if supported in your regex implementation.


# Looking around

The last & thoughest feature each developer should definitely know: lookahead & lookbehind!

![Lookahead vs Lookbehind syntax](/assets/blog-images/regex-lookaround.png "Lookahead vs Lookbehind syntax")

**Example**:

```
A(?=B)   – Literal A followed by B
A(?!B)   – Literal A not followed by B
```

Lookarounds do not match anything in themselves which make them very handy
when you want to replace some text but only when it is (not) preceded/succeeded by something else.
