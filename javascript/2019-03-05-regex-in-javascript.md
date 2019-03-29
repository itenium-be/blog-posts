---
layout: post
author: Wouter Van Schandevijl
title:  "JavaScript RegExp Tutorial"
date:   2019-03-05 00:00:00
desc: >
  A tutorial and cheat-sheet on RegExp testing, matching and replacing in JavaScript.
img: when-searching-regular-expressions-on-unsplash.jpg
imgdesc: Unsplash picture by Aga Putra
imgurl: https://unsplash.com/photos/7MBgGXQ2Rqg
categories: javascript
tags: [cheat-sheet,regex]
interesting:
  - git: aloisdg/awesome-regex
    desc: "A curated collection of awesome Regex libraries, tools, frameworks and software"
  - url: https://www.rexegg.com/
    desc: "RexEgg.com: The world's most tyrannosaurical regex tutorial"
  - url: http://2ality.com/2015/07/regexp-es6.html
    desc: "New regular expression features in ECMAScript 6 (7/2015)"
  - git: tc39/proposal-string-matchall
    desc: "Currently in Stage 3: Candidate (3/2019)"
  - url: https://regexr.com/
    desc: "regexr.com: Online, open source tool to learn, build, & test regexes"
  - url: https://regex101.com/
    desc: "regex101.com: Another online regex test tool"
toc:
  title: JavaScript RegExp
  icon: icon-script
todo:
  - reason: Followup
    url: http://github.com/tc39/proposal-string-matchall
    desc: "Check if this got to Stage 4 yet"
  - reason: Too advanced
    url: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace
    desc: "Replace also works with a function argument for capture side effects"
---

A cheat sheet for the regex syntax in JavaScript.


[MDN RegExp Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
{: style="float: right"}

**TL&DR**
```javascript
''.test(/^$/);
'ok'.replace(/(.)(.)/g, '$2$1'); // $$, $&, $`, $'

const matchG = 'aaa'.match(/a/g);
matchG == ['a', 'a', 'a'];

const matchNoG = 'str'.match(/(st)r/);
matchNoG == Object.assign(['str', 'st'], {groups: undefined, index: 0, input: 'str'});
matchNoG == /(st)r/.exec('str');
```

<!--more-->



# Common Usage

Define yourself a regex:  
```javascript
// Preferred way
const expressionLiteral = /\w+/;

// Only use this for dynamic patterns
const ctor = new RegExp('\\w+');
```

## RegExp.prototype.test

[RegExp.prototype.test](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)
{: style="float: right"}

Returns `true` or `false`.

```javascript
const didMatch = /a/.test('abc');
```



## String.prototype.replace

[String.prototype.replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
{: style="float: right"}

Without the `g` flag, only the first match is replaced. (which makes no difference in the example:)

```javascript
'dog The'.replace(/(dog) (The)/g, '$2 $1');
```

| Replacement   | Description
|---------------|------------
| `$$`          | A literal $
| `$&`          | The matched string
| ``$` ``       | Portion before the match
| `$'`          | Portion after the match
| `$n`          | With `n < 100`: the nth captured group (!! 1 indexed !!)
{: .table-code}




## String.prototype.match

**match() with the `g` flag**:  

[String.prototype.match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)
{: style="float: right"}

Returns `null` or all matches as a `string[]`. There is no captured group info.  

```javascript
const match = 'a_ab_a'.match(/a(b?)/g);
expect(match).toEqual(['a', 'ab', 'a']);
```


**match() without the `g` flag**:  
Returns `null` or the first match and its capturing groups.  
The result is an array with additional fields
(groups, index and input). `exec` 

<small>(Hence the weird toEqual array syntax in the code below...)</small>

```javascript
const match = '0abaa'.match(/a(?<theB>b?)/);
expect(match).toEqual([
    0: 'ab', // Entire matched string
    1: 'b',  // First captured group
    groups: {theB: 'b'}, // Results of named groups (ES2018)
    index: 1,
    input: '0abaa'
]);

// Without the g flag, match behaves exactly like exec:
const exec = /a(?<theB>b?)/.exec('0abaa');
expect(exec).toEqual(match);
```

**!! [RegExp.prototype.matchAll](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@matchAll) is "Under Construction" !!**  

<!-- Fun aligning with " " (=U+2000, En Quad, &#8192;) -->

🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 👷 👷 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧

🚧 👷 🚧  [proposal-string-matchAll](https://github.com/tc39/proposal-string-matchall) is a Stage 3 Candidate at the moment 🚧 👷 🚧

🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 👷 👷 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧 🚧


## RegExp.prototype.exec

[RegExp.prototype.exec](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)
{: style="float: right"}

When you need the capturing groups of all matches, it's `exec` to the rescue.

```javascript
const globbing = /(a)(b2?)/g;
const input = 'ab_ab2';
// Indexes     0  3  

globbing.exec(input);
== Object.assign(['ab', 'a', 'b'], {index: 0});

globbing.exec(input);
== Object.assign(['ab2', 'a', 'b2'], {index: 3});

globbing.exec(input);
== null
```




# Flags

```javascript
const rl = /ab+c/i;
const rc = new RegExp('ab+c', 'i');
```

**Available flags**:  

| Flag       | Property    |  Remarks | StackOverflow
|------------|---------------------------------------------------------------------------------------------------
| `i`        | [.ignoreCase](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/ignoreCase) | Case insensitive
| `g`        | [.global](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/global)     | Do not stop at first match but find all of them
| `m`        | [.multiline](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/multiline)  | `^` and `$` match beginning/end of each line (otherwise of entire string) | [StackOverflow](https://stackoverflow.com/questions/1979884/how-to-use-javascript-regex-over-multiple-lines)
| `s`        | [.dotAll](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/dotAll)     | `.` matches newlines. (ES2018)
| `u`        | [.unicode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode)    | `/^.$/u.test('😀')`
| `y`        | [.sticky](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky)     | Use `.lastIndex` to match at that specific index **only** (overwrites the `g` flag) | [StackOverflow](https://stackoverflow.com/questions/4542304/what-does-regex-flag-y-do)
|            | [.flags](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/flags)      | Returns a string with the active flags
{: .table-code}





# Less Common

```typescript
const flags: string = /a/ig.flags; // "gi"
const src: string = /a/.source; // "a"
```

## String.prototype.search

[String.prototype.search](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/search)
{: style="float: right"}

A more powerful version of [`indexOf`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf). Returns `-1` if no match.

```javascript
const index: number = /a/.search('a');
```



## String.prototype.split

[String.prototype.split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)
{: style="float: right"}

Usually used in the form of something like `'a,b,c'.split(',')`.
But also possible to split on regex matches.

```javascript
"a,b;c".split(/,|;/);
// --> ['a', 'b', 'c']

// Wrap in parentheses to include the separator.
"a,b;c".split(/(,|;)/);
// --> ['a', ',', 'b', ';', 'c']
```



## RegExp.prototype.lastIndex

[RegExp.prototype.lastIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex)
{: style="float: right"}

Used by `exec`, `test`, ... with the global (`g`) flag and for any of the above functions when using the sticky (`y`) flag.

```javascript
const input = 'aab';
const regex = /a/g;
regex.test(input); // Returns true. lastIndex is now 1
regex.test(input); // Returns true. lastIndex is now 2
regex.test(input); // Returns false. lastIndex is reset to 0
```
