---
layout: post
author: Wouter Van Schandevijl
title:  "Array.prototype for .NET developers"
date:   2019-02-24 00:00:00
desc: >
  A guide on learning JavaScript `Array.prototype` by comparing it to C# Linq.
img:
  url: Array.prototype.png
bigimg:
  url: Array.prototype-big.png
  prompt: "array prototype"
  origin: Midjourney
categories: javascript
tags: [cheat-sheet,tutorial]
extras:
  - githubproject: https://github.com/itenium-be/Array.prototype
    githubtext: Github source code as UnitTests
  - githubproject: https://github.com/itenium-be/ModernJS
    githubtext: Jest UnitTests for all ECMAScript features since 2019
interesting:
  - git: msn0/mdn-polyfills
    desc: "Include polyfills for your favourite functions that are not (yet) implemented by your browser(s)."
  - url: http://2ality.com/
    desc: "2ality.com: Blogging on ES proposals"
  - url: http://es6-features.org
    desc: "es6-features.org: Overview and Comparison"
  - url: https://stackoverflow.com/a/200614/540352
    desc: "StackOverflow: Jon Skeet on why there is no forEach in Linq"
  - git: morelinq/MoreLINQ
    desc: "Extensions to LINQ to Objects"
  - url: https://esdiscuss.org/topic/having-a-non-enumerable-array-prototype-contains-may-not-be-web-compatible
    desc: "MooTools: Why Contains is called includes in JavaScript"
toc:
  title: Array.prototype
  icon: icon-javascript
last_modified_at: 2024-06-01 00:00:00 +0200
updates:
  - date: 2024-06-01
    desc: "Added Object.groupBy"
  - date: 2023-10-06
    desc: "More updates for ECMAScript 2023"
  - date: 2023-09-04
    desc: "Added flat and flatMap"
  - date: 2023-03-08
    desc: "Added Distinct, flatMap"
---

The [`Array.prototype`][prototype] functions have been available for a long time but
it's since [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
that "Linq lambda syntax"-like functionality is available in JavaScript.

This blog post explains the most common functions by comparing them to their C# equivalents.

A basic example:  
```javascript
// JavaScript
const result = [0, 1, 2, 3, null]
    .filter(x => x !== null)
    .map(x => x * 10)
    .sort((a, b) => b - a);

expect(result).toEqual([30, 20, 10, 0]);

// C#
var result = new int?[] {0, 1, 2, 3, null}
    .Where(x => x != null)
    .Select(x => x * 10)
    .OrderByDescending(x => x)
    .ToArray();
```

<!--more-->

Whenever you want to write a `for` to loop over an array, the `Array.prototype` functions
probably allow you to achieve the same result but in more functional and succinct manner.

Do note that the deferred execution we know from Linq does not apply to `Array.prototype`!

# Comparison Table


| C#                              | JavaScript                                     | MDN Link
|---------------------------------|----------------------------------------------------------
| Select()                        | <b>map</b>((cur, index, array): any)           | [map][map]
| Where()                         | <b>filter</b>((cur): boolean)                  | [filter][filter]
| Contains()                      | <b>includes</b>(value, fromIndex)              | [includes][includes]
| FirstOrDefault()                | <b>find</b>((cur): boolean): undefined \| any  | [find][find] / [findIndex][findIndex]
| LastOrDefault()                 | <b>findLast</b>((cur): boolean): undefined \| any | [findLast][findLast] / [findLastIndex][findLastIndex]
| All()                           | <b>every</b>((cur): boolean): boolean          | [every][every]
| Any()                           | <b>some</b>((cur): boolean): boolean           | [some][some]
| Concat()                        | <b>concat</b>(arr: any[]): any[]               | [concat][concat]
| Skip(start).Take(start - end)   | <b>slice</b>(start = 0, end = length-1)        | [slice][slice]
| string.Join()                   | <b>join</b>(separator = ',')                   | [join][join]
| Array.IndexOf()                 | <b>findIndex</b>((cur): boolean): -1 \| number | [findIndex][findIndex]
| ElementAt()                     | <b>at</b>(index) (accepts negative indexes)    | [at][at]
| Count()                         | <b>length</b>: number                          | [length][length]
| SelectMany()                    | <b>flat</b>(levels = 1) / <b>flatMap</b>(fn)   | [flat][flat] / [flatMap][flatMap]
| GroupBy()                       | <b>Object.groupBy</b>(arr, groupFn)            | [groupBy][groupBy]
| Distinct()                      | <b>filter</b>((el, i, arr) => arr.indexOf(el) === i)
| Extension method                | forEach((cur): void): void                     | [forEach][forEach]
| 
| **Mutating in JS**              | These are [in place][in-place] operations      |
| OrderBy()                       | <b>sort</b>((a, b): number)                    | [sort][sort] / [toSorted][toSorted]
| Reverse()                       | reverse()                                      | [reverse][reverse] / [toReversed][toReversed]
|---------------------------------|------------------------------------------------|----------
{: .table-code}


# Commonly used methods

## Select = map

```javascript
// C#
var result = enumerable.Select(x => x);

// JavaScript
let result = array.map(x => x);

// Using spread to avoid mutation
const input = [{k: 1, v: true}, {k: 2, v: false}];
const result = input.map(x => ({...x, v: !x.v}))
```

When mapping to an object without code block, you need to wrap your object between extra parentheses like

```javascript
[0, 1].map(x => ({value: x}));

// Because without the extra parentheses
[0, 1].map(x => {value: x});
// --> [undefined, undefined]

// it is the same as writing:
[0, 1].map(x => {
  value: x; // No error, because JavaScript?
  return undefined;
});
```


## Where, Distinct = filter

`Where` and [`filter`][filter] behave pretty much exactly alike.
Linqs `Distinct` we'll need to implement ourselves.

```javascript
const input = [0, 0, 1, 5, 5];
// Equals true when the first occurrence of the value is the current value
const result = input.filter((element, index, array) => array.indexOf(element) === index);
// Or: [...new Set(input)];
expect(result).toEqual([0, 1, 5]);
```


## Aggregate, GroupBy, ... = reduce

Linq has Sum, Min, Max, Average, GroupBy, etc.
While JavaScript doesn't have them, they can all be achieved trivially with [`reduce`][reduce]

<!-- [apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) -->

**Sum, Min, Max, Average**:  
```javascript
const input = [0, 1];
const sum = input.reduce((total, cur) => total + cur, 0);
const average = sum / input.length;
const min = Math.min.apply(Math, input); // Old school
const min = Math.min(...input); // Using spread
```

**Aggregate, GroupBy**:  
```javascript
const input = [0, 1, 2, 3];

const result = input.reduce((acc, cur) => {
  if (cur % 2 === 0) {
    acc.even.push(cur);
  } else {
    acc.odd.push(cur);
  }
  return acc;
}, {even: [], odd: []});

expect(result).toEqual({even: [0, 2], odd: [1, 3]});
```

Since 2024, JS has a dedicated `Object.groupBy`:

```js
const socks = [
  { name: 'JavaScript', type: 'lang' },
  { name: 'Angular', type: 'package' },
  { name: 'React', type: 'package' },
];

const grouped = Object.groupBy(socks, sock => sock.type);

expect(grouped).toEqual({
  lang: [{ name: 'JavaScript', type: 'lang' }],
  package: [
    { name: 'Angular', type: 'package' },
    { name: 'React', type: 'package' },
  ]
})
```


## slice

Linq has First, Last, Skip, SkipLast, SkipWhile, Take, TakeLast, TakeWhile.
JavaScript has [`slice`][slice].

```javascript
// Shallow copy
const input = [0, 1, 2, 3];
expect(input.slice()).toEqual([...input]);

// Signature
slice(startIndex = 0, endIndex = length-1);
```

### C# Ranges

```c#
var array = new[] { 1, 2, 3, 4, 5 };

Assert.Equal(new[] { 3, 4 }, array[2..^1]);
Assert.Equal(new[] { 1, 2 }, array[..^3]);
Assert.Equal(new[] { 3, 4, 5 }, array[2..]);
Assert.Equal(new[] { 1, 2, 3, 4, 5 }, array[..]);
```

### with

Since 2023, there is also [`with`][with] which returns a new
array where one item is replaced with a new value by index.

```js
const newArr = arr.with(index, newValue);
```




# Mutations

These functions operate in place.  
Since ECMAScript 2023, some have non-mutating alternatives!

## OrderBy = sort and toSorted

- Use [toSorted][toSorted] if you need a new array for the sorted result.
- **Without compareFn the array is sorted according to each character's Unicode code point value.**
- The compareFn should return a number:
    - `-1` (or any negative number): `a` comes **before** `b`
    - `0`: Equal
    - `1` (or any positive number): `a` comes **after** `b`


```javascript
// Numbers
[10, 5].sort(); // [10, 5]: each element is converted to a string
[10, 5].sort((a, b) => a - b); // [5, 10]

// Strings
['z', 'e', 'é'].sort(); // ['e', 'z', 'é']
['z', 'e', 'é'].sort((a, b) => a.localeCompare(b)); // ['e', 'é', 'z']

// Dates
[d1, d2, d3].sort((a, b) => a.getTime() - b.getTime());
```

**C#**  
- The signature is a bit different: `OrderBy(Func<TSource, TKey> keySelector, IComparer<TKey> comparer)`
- `OrderBy` vs `OrderByDescending`: switch `a` and `b`...
- JS doesn't have `ThenBy` chaining possibilities, you'll have to do this yourself.


## forEach

While [`forEach`][forEach] is not really doing any mutation by itself, it is often what it's used for.
Mutations are especially dangerous in for example a Redux environment where UI changes might lag.
The same can usually be achieved with `map`.

```javascript
const input = [{value: 1, visited: 0}, {value: 2, visited: 0}];

// Potentially dangerous
input.forEach(el => {
    el.visited++;
});

// ...el will create a shallow copy only!
const result = input.map(el => ({...el, visited: el.visited + 1}));

// Or use the good old loop?
for (let itm of input) {
    console.log(itm.value);
}
```


## Other mutators

- [`reverse()`][reverse]: There is now also [`toReversed()`][toReversed] (2023)
- [`push(el1, [el2, ...])`][push]: Add element(s) at the end. Returns the new array length.
- [`shift()`][shift]: Remove the first element at the start. Returns the removed element.
- [`unshift(el, [el2, ...])`][unshift]: Add element(s) at the start. Returns the new array length.
- [`splice(start, [deleteCount, [el1, [el2, ...]]])`][splice]
    - The swiss army knife: add and/or remove element(s)
    - Returns array of removed elements (or empty array)
    - There is now also [toSpliced][toSpliced] (2023)


# Linq for ages 5 and up

Thanks [Wim De Cleen](https://x.com/bewidec/status/935573858136051713) (and Martin Fowler)
for this amazing diagram, I love it!

![Explain LINQ to a 5 year old](/assets/blog-images/Array.prototype-fowler-diagrams.jpg "Explain LINQ to a 5 year old"){: .img-responsive}



[in-place]: https://en.wikipedia.org/wiki/In-place_algorithm
[reverse]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse
[push]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push
[shift]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift
[unshift]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift
[forEach]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach
[slice]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice
[splice]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice
[reduce]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce
[prototype]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array
[filter]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter
[map]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map
[every]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every
[some]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some
[find]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find
[findIndex]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex
[includes]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes
[concat]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat
[length]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length
[sort]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort
[join]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join
[flat]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat
[flatMap]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap
[findLast]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findLast
[toSorted]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/toSorted
[at]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/at
[toReversed]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/toReversed
[with]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/with
[toSpliced]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/toSpliced
[findLastIndex]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findLastIndex
[groupBy]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/groupBy
