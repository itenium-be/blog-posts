---
layout: post
author: Wouter Van Schandevijl
title:  "Regular Expressions: tutorial"
date:   2018-03-28 00:00:00 +0200
desc: >
    Regex tutorial
img: none.png
categories: productivity
tags: [regex,tutorial,cheat-sheet]
series: regexes
toc:
    title: Tutorial
---

WIP: we zaten hier...
- http://www.regular-expressions.info/tutorialcnt.html
- http://regexlib.com/CheatSheet.aspx?AspxAutoDetectCookieSupport=1
- https://www.cheatography.com/davechild/cheat-sheets/regular-expressions/
- http://www.rexegg.com
- https://www.debuggex.com/cheatsheet/regex/javascript

On how to match stuff.


book Mastering Regular Expressions
- starts really slow so it is perfectly readable by someone with even no (or very little) regex experience
- 



links
- rex egg
- reginfo
- link from book?

tool regex from book?

<!--more-->

## Options

i
g


## A picture

a diagram here... wouldn't that be fun


## Syntax



| Regex      | Matches
|------------|--------------------------|
| abc        | 'abc'
| ^s         | 's' but only at the start of the input
| e$         | 'e' but only at the end
| \\$        | Escape character: Match '$' anywhere in the input
| .          | 
| [az]       | 'a' or 'z'
| [0-9]      | A single digit | 
| [a-zA-Z]   |
| [a-Z]      | This might also include '[]\\^_`'
| [\n-]      | A newline or a hyphen
| [^a]       | Everything but 'a'
| (ab\|yz)   | 'ab' or 'yz'. () = group
| (?:ab)     | 'ab' but non-capturing group
{: .table-code}


\d
\Qlite[]ral\E --> Only for Perl, PHP, Java, SublimeText, ...

(), (?:)


{}, *, ?, +




Regular Expression Replacement
$$	Inserts $
$&	Insert entire match
$`	Insert preceding string
$'	Insert following string
$Y	Insert Y'th captured group



## Looking around

this(?=must be after) = lookahead
this(?!must not be after)

(?<=must be before)this = lookbehind
(?<!must not be before)this

<(backreference)>\w*</\1>

[advanced-cheat-sheet]: https://www.cheatography.com/davechild/cheat-sheets/regular-expressions/
