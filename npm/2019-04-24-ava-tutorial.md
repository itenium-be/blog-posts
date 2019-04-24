---
layout: post
author: Wouter Van Schandevijl
title:  "AVA Tutorial"
date:   2019-04-24
desc: >
  The unicorn JavaScript testing framework by Sindre Sorhus.
bigimg:
  url: ava-big.png
img: ava.jpg
imgdesc: Photo by Marco Secchi
imgurl: https://unsplash.com/photos/yjq31QGOnSY
categories: javascript
tags: [testing,tutorial]
interesting:
  - git: avajs/awesome-ava
    desc: "Awesome AVA"
toc:
  title: 👓 AVA Tutorial
package-versions:
  ava: 1.4.1
todo:
  - "t.snapshot()"
  - "CLI: `--update-snapshots`, `-u`: Update snapshots"
  - "https://github.com/avajs/ava/blob/master/docs/04-snapshot-testing.md"
  - "option: snapshotDir"
---

{% include github-stars.html url="avajs/ava" desc="🚀 Testing can be a drag. AVA helps you get it done." %}

<br>
A concurrent test runner from the Andromeda galaxy. If I was to stray from 
[Jasmine](https://itenium.be/blog/javascript/javascript-testing-jasmine-getting-started/) then it could only 
be for a project [Sindre Sorhus](https://github.com/sindresorhus) is working on :)
The screenshots on the project site promised extensive assertion failure output with clean stack traces
and built-in Promise, async/await, Observable and React component support.
So I tried it out and yup, AVA delivered.

# Get Started
{: .hide-from-excerpt}

```bash
# Install
npm init ava

# Run tests
npx ava --watch --verbose
```

<!--more-->

AVA runs automatically ignore:  
- Directories called `fixtures`, `helpers` or `node_modules`
- Files starting with an underscore (`_`)


Checkout the [recipes](https://github.com/avajs/ava#recipes) for examples when working with 
[TypeScript](https://github.com/avajs/ava/blob/master/docs/recipes/typescript.md),
[Flow](https://github.com/avajs/ava/blob/master/docs/recipes/flow.md),
[Babel](https://github.com/avajs/ava/blob/master/docs/recipes/babel.md),
[React](https://github.com/avajs/ava/blob/master/docs/recipes/react.md),
[Vue.js](https://github.com/avajs/ava/blob/master/docs/recipes/vue.md),
Debugging with [Chrome DevTools](https://github.com/avajs/ava/blob/master/docs/recipes/debugging-with-chrome-devtools.md)
and [WebStorm](https://github.com/avajs/ava/blob/master/docs/recipes/debugging-with-webstorm.md)
and much more...
There is also a [Common Pitfalls](https://github.com/avajs/ava/blob/master/docs/08-common-pitfalls.md) document should
you run into some trouble early on.



# Example

```javascript
import test from 'ava';

// Parameter needs to be called `t`
// for enhanced assertion messages.
test('Basic test example', t => {
    // Test will fail if not exactly
    // two assertions are made
    t.plan(2);
    t.pass();
    // Skipped tests still count to the plan
    t.true.skip();


    t.log(...values);


    // Test fails if exceeded.
    // Resets after each assertion.
    // There is no default timeout.
    // Globally from CLI: --timeout=10s
    t.timeout(ms);
});


// HOOKS:
// Runs before the first test in the file
// Runs before beforeEach hooks, if any.
test.before('optional title', t => {
    t.context.data = 'Shared with the tests';
    // t.context is an object by default but may be overwritten
});

// Runs after the last test in the file
test.after(() => {});

// Individual test setup/teardown
test.beforeEach(() => {});
test.afterEach(() => {});

// Will run even if other hooks or the test fails
test.afterEach.always(() => {});
// Also: test.after.always()

// Hooks run concurrently by default, unless:
test.serial.before(() => {});
```

[Assertions](https://github.com/avajs/ava/blob/master/docs/03-assertions.md)
{: .title-url}

# Assertions

You can use any assertion library
instead of or in addition to the built-in one,
provided it throws exceptions on failures.

All built-in assertions have a last optional `message` parameter.

<!-- [Object.is()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) -->

```javascript
import test from 'ava';

test('Built-in', t => {
    t.pass('optional message');
    t.fail();

    t.true(value); // t.false()
    t.truthy(value); // t.falsy()

    t.is(value, expected); // Object.is() comparison
    t.not(value, expected); // Inverted t.is()

    t.regex(contents, regex); // Also: notRegex()

    t.deepEqual(value, expected); // Also: notDeepEqual()
});
```

#### power-assert

{% include github-stars.html url="power-assert-js/power-assert" desc="Very fancy assertion failure reporting" %}

```javascript
// Outputs failures with power-assert when value is falsy
t.assert(value, [message]);
```

## Macros

Create custom testers with [macros](https://github.com/avajs/ava/blob/master/docs/01-writing-tests.md#reusing-test-logic-through-macros).

```javascript
function macro(t, input, expected) {
  t.is(input, expected);
}
test('static title', macro, 'input', 'expected');


// Dynamic Titles
macro.title = (providedTitle = undefined, input, expected) => '';

test(macro, 'input', 'expected');
test('providedTitle', macro, 'in', 'expected');
```


## Exceptions

```javascript
test('throws error', t => {
    const err = t.throws(fn, [expected]); // Also: .notThrows()
    // with expected either
    // - A constructor (instanceof)
    // - A string (error.message comparison)
    // - A RegExp (error.message match)
    // - A matcher object. Ex:
    const matcher = {
        instanceOf: TypeError,
        is: 'Object.is()',
        message: 'string | RegExp',
        name: 'err.name',
        code: 'err.code',
    };
    // When omitting expected, you still need to
    // pass null if you want to set the message.
});


test('throws async', async t => {
    await t.throwsAsync(async () => {
        throw new TypeError('oops');
    }, {instanceOf: TypeError, message: 'oops'});
    // Also: .notThrowsAsync()
    
    const error = await t.throwsAsync(() => Promise.reject('aargh'));
    t.is(error.message, 'aargh');
});
```

## Not included

- [Endpoint testing Recipe](https://github.com/avajs/ava/blob/master/docs/recipes/endpoint-testing.md): Use your favourite library
- [Browser testing](https://github.com/avajs/ava/blob/master/docs/recipes/browser-testing.md)
- Anything else you might need? Someone might have done it already: [Awesome AVA](https://github.com/avajs/awesome-ava)




# Workflow

```javascript
test.only('Like Jasmine fit', t => {
    t.pass();
});

test.skip('Like Jasmine xit', t => {
    t.pass();
});

test.todo('Really need to write this test');

test.failing('Passes only when the test does NOT pass', t => {
    t.fail();
});
```




[Command Line](https://github.com/avajs/ava/blob/master/docs/05-command-line.md)
{: .title-url}

# CLI

Always runs the local AVA installation if available.  
CLI takes precedence over configuration.

```bash
npx ava --help

# Default arguments
npx ava test.js test-*.js test/**/*.js **/__tests__/**/*.js **/*.test.js

npx ava
    --watch     # or -w 
    --verbose   # or -v: Outputs all test titles
    --match     # or -m: Run only matching test titles
    --timeout   # or -T: in ms. Or: "30s" (30 seconds), "3m" (3 minutes)
    --fail-fast # Stop on first failure

# Case insensitive test title matching
# Overrides .only
npx ava -m='startWith*' -m='!*notContains*' -m='exact'
```

{% include github-stars.html url="sindresorhus/matcher" desc="Matcher used for `--match`" %}

#### Other CLI parameters

- `--serial`, `-s`: Run tests serially
- `--concurrency`, `-c`: Max tests running at the same time (Default: # CPU Cores)
- `--tap`, `-t`: Generate [Test Anything Protocol](https://en.wikipedia.org/wiki/Test_Anything_Protocol) output
- `--color` and `--no-color`: With color by default
- `--reset-cache`






[Configuration](https://github.com/avajs/ava/blob/master/docs/06-configuration.md)
{: .title-url}

# Configuration

In your `package.json`:  
```json
{
    "ava": {
        "files": [
            "my-test-directory/**/*.js",
            "!my-test-directory/exclude-this-directory/**/*.js",
            "!**/exclude-this-file.js"
        ],
        "sources": [ // changes to matching files re-run tests while in --watch mode
            "**/*.{js,jsx}",
            "!dist/**/*"
        ],
        "failWithoutAssertions": false, // True by default: a test without assertion fails
        "compileEnhancements": false,   // Disable power-assert
        "require": [
            "@babel/register"
        ],
        "babel": {
            "extensions": ["jsx"],
            "testOptions": {
                "babelrc": false
            }
        },
        "a-cli-arg": "ex: 'failFast': true"
    }
}
```

- Can also add json nodes matching the CLI parameters for failFast, tap, verbose, timeout and `match: string[]`
- `cache`: `true`: Use cache in `node_modules/.cache/ava`. `false`: Use temp dir.
- Also possible to create a `ava.config.js` in the same folder as your `package.json`.





# Additional Features

```javascript
test('Promise support', t => {
    return Promise.resolve(true).then(result => {
        t.true(result);
    });
});

test('async/await support', async t => {
    const value = await Promise.resolve(true);
    t.true(value);
});

test('Observable support', t => {
    return of(1).pipe(map(() => t.pass()));
});

test.cb('Callback support with t.end', t => {
    // Any truthy value passed as first argument
    // to `t.end` will fail the test
    fs.readFile('data.txt', t.end);
});


test.afterEach.cb('This stuff also works for hooks', t => {
    setImmediate(t.end);
});


console.log('Currently runing', test.meta.file);
```
