---
layout: post
author: Wouter Van Schandevijl
title:  "JavaScript Testing: Jasmine customization"
date:   2017-08-06 00:00:00 +0200
desc: >
    Customize Jasmine to your project needs with custom matchers, custom equality testers.
    Test legacy code by providing Jasmine with global variables.
img:
  url: jasmine4.jpg
  origin: https://unsplash.com/photos/qGhYl8-ktz8
  desc: Photo by David Brooke Martin
categories: javascript
tags: [tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/itenium-be/jasmine-tut
interesting:
  - git: JamieMason/Jasmine-Matchers
    desc: A whole bunch of generic custom matchers
  - git: mjeanroy/jasmine-utils
    desc: Set of custom matchers for Jasmine Test Framework
updates:
  - date: 2023-03-28 05:00:00 +0200
    desc: Updated to Jasmine 4.3.0.
toc:
    title: Customization
    icon: icon-javascript
package-versions:
  - package: jasmine
    version: 4.3.0
---

Make your Jasmine experience even more luscious with custom
matchers and global convenience methods.  
Mock global variables in your modules with `jasmine.getGlobal().pi = 3.14`.

<!--more-->

# Custom equality testers

If custom equality testers are provided, they will be checked first 
before the default equality logic when calling `toEqual()`.

```js
beforeEach(function() {
  const myCustomEquality = function(first, second) {
    if (first === 'everything' && typeof second === 'number') {
      // return true or false as final result of .toEqual()
      return second === 42;
    }
    // return undefined to let the next equality tester give it a shot
    return undefined;
  };

  jasmine.addCustomEqualityTester(myCustomEquality);
});

it('should use myCustomEquality', function() {
  expect('everything').toEqual(42);
});

it('should not use myCustomEquality', function() {
  expect('something').not.toEqual(42);
});
```



# Custom matchers

```js
beforeEach(function() {
  const customMatchers = {
    toBeDivisibleBy: function(util, customEqualityTesters) {
      return {
        compare: function(actual, expected) {
          var result = {pass: (actual % expected) === 0};
          if (!result.pass) {
            // failure message
            result.message = `${actual} is not divisible by ${expected} (mod: ${actual % expected})`;
          } else {
            // failure message - when .not.toBeDivisibleBy() fails
            // if a negativeCompare function is provided, this message will never be displayed
            result.message = `${actual} is divisible by ${expected} (mod: ${actual % expected})`;
          }
          return result;
        },
        // Optional function when you need more control for the .not case
        //negativeCompare: function(actual, expected) {}
      };
    }
  };

  jasmine.addMatchers(customMatchers);
});

it('is divisible by 2', function() {
  // The parameter names of the compare function
  const actual = 14;
  const expected = 2;
  expect(actual).toBeDivisibleBy(expected);
});
```

The customMatcher functions parameters are [`util`][github-matchersUtil] and `customEqualityTesters`,
the latter is passed to utils functions.

```js
const util = {
  equals: function(a, b, customEqualityTesters, diffBuilder),
  contains: function(haystack, needle, customEqualityTesters),
};
```



# Environment and globals

Files included in the `jasmine.json` "helpers" wildcard(s)
can provide convenience functions, global variables as well as
custom equality testers and matchers.

Helpers.js:
```js
beforeEach(function() {
  // Custom equality testers and matchers
  const myExternalCustomEquality = function(first, second) {
    if (first === 'wuuk' && typeof second === 'number') {
      return second === 42;
    }
  };

  jasmine.addCustomEqualityTester(myExternalCustomEquality);
  //jasmine.addMatchers(customMatchers);


  // Convenience fuctions, available in it, beforeEach, etc
  this.yourHelper = function() {
    return 'as defined';
  };


  // Mock global variables in required modules
  jasmine.getGlobal().t = t => t;
});
```


Some suite:
```js
describe('Convenience stuff loaded by spec/helpers/helpers.js', function() {
  it('should use myExternalCustomEquality', function() {
    expect('wuuk').toEqual(42);
  });


  it('can provide helper functions', function() {
    expect(this.yourHelper()).toBe('as defined');
  });


  it('can set global variables on jasmine.getGlobal()', function() {
    const globalz = require('./global.js');
    //global.js:
    // module.exports = {aGlobalUsingFunction: val => t(val)};
    //helpers.js:
    // jasmine.getGlobal().t = t => t;
    expect(globalz.aGlobalUsingFunction('ouch')).toBe('ouch');
  });
});
```


Some global Jasmine settings:
```js
it('has default timeout of 5000ms', function() {
  expect(jasmine.DEFAULT_TIMEOUT_INTERVAL).toBe(5000);
});

it('can change the pretty printing limits', function() {
  expect(jasmine.MAX_PRETTY_PRINT_ARRAY_LENGTH).toBe(50);
  expect(jasmine.MAX_PRETTY_PRINT_DEPTH).toBe(8);
  expect(jasmine.MAX_PRETTY_PRINT_CHARS).toBe(1000);
});

it('has its own jasmine.getEnv()', function() {
  expect(Object.keys(jasmine.getEnv())).toContain('beforeEach');
});
```


[github-matchersUtil]: https://github.com/jasmine/jasmine/blob/main/src/core/matchers/matchersUtil.js
