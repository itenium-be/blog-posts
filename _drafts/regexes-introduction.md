---
layout: post
title:  "Regular Expressions: introduction"
date:   2017-05-30 12:00:00 +0200
categories: productivity
tags: [regex]
series: regexes
interesting:
  - http://www.rexegg.com
  - http://www.regular-expressions.info
  - https://www.cheatography.com/davechild/cheat-sheets/regular-expressions/ (or another cheat sheet?)
---

Some people, when confronted with a problem, think 'I know, I'll use regular expressions.' Now they have two problems.  
([source: Jeffrey Friedl's Blog][jeffrey-friedl])
{: .notice .hide-from-excerpt}

Regular Expressions are typically used for user input validation.
Another good fit is extracting and/or reorganising strings.
Replace a dozen lines of Substring and IndexOf with one 'pretty' regex!

Other usage in code might be suspect: does the readability really benefit from the regex
or does it just make things more complex.

Be sure to have Unit Tests for all input you'd like to support.  
You'll need them whenever requirements change or bugs need to be fixed.
{: .notice--info .hide-from-excerpt}

<!--more-->

Even with this seemingly limited applicability - if you don't want to shoot yourself in the foot that is -
Regexes are still a very useful tool as they can be used in many one time actions.
- Find in (all project) files in your IDE
- Quickly find the interesting stuff in big files (think logs, data dumps etc)
- Convert data that came from A to what B likes
- Many a CLI tool or its parameters understand regex

These are the reason this series is posted under the productivity category.

**An example**

Whenever I fix a bug that was the result of an error that might have happened in other places aswell,
I might use a regex to find those, even if the other code contains different variables, styling etc it
would still find them - at least if the regex is bug free.


**Tooling**

Don't forget about tooling.
Having direct feedback on what your regex is matching on your data set as you type it.. It's the best :)


**Moving on**

Did I already mention that you'll want to have tests of the code using regexes, especially if they tend
to change often? Don't be shy to write them, even if they are the only ones in your repository.


[parse-html-with-regex]: https://stackoverflow.com/a/1732454/540352
[jeffrey-friedl]: http://regex.info/blog/2006-09-15/247
