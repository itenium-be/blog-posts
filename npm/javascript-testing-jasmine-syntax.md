---
layout: post
title:  "JavaScript Testing: Jasmine syntax"
date:   2017-05-30 12:00:00 +0200
categories: javascript
tags: [javascript,tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/be-pongit/jasmine-tut
---

{% include toc title="Jasmine Syntax" icon="icon-javascript" %}

The basic example contains the general test suite structure and the 
two most used matchers `toBe()` (===) and `toEqual()` (deep compare),
followed by all the other matchers that come out of the box.

To finish some helpers for your workflow: how to only have certain
tests run and how to exclude tests.

<!--more-->

## Basic example

Time to start writing some real tests with Jasmine!

```js
describe('basic example', () => {
	beforeEach(() => {
		this.result = 1;
	});

	it('can hardly fail', () => {
		// toBe() uses ===
		expect(this.result).toBe(1);

		// For primitive types, toEqual
		// behaves the same way as toBe
		// toEqual goes further however
		// by deeply comparing objects.
		expect({a: {}}).toEqual({a: {}});
		expect([0, {a: 1}]).toEqual([0, {a: 1}]);
	});

	afterAll(() => {});
});
```

A `describe` groups one or more related tests together in a suite. Describes can be nested indefinitely
and contain one `it` for each test in the suite.

`beforeAll` and `afterAll` runs once before/after all tests in the containing describe are executed.
`beforeEach` and `afterEach` runs once before/after each test is executed.

The `this` context is shared in the beforeX/afterX functions and the `it`s, allowing you to setup/teardown stuff
and access them in your tests.




## Matchers

Set expectations with matchers. Usage and differences of `toBe` and `toEqual` are already covered
in the basic example above. These are the other matchers:

```js
it('can negate any matcher with .not', () => {
	expect(1).not.toBe(0);
});

it('knows about regexes', () => {
	expect('some bar').toMatch(/bar$/);
	expect('some bar').toMatch('\\sbar');
});

it('can check numbers', () => {
	expect(5).toBeLessThan(10); // .toBeLessThanOrEqual(5);
	expect(5).toBeGreaterThan(1); // .toBeGreaterThanOrEqual(5);

	const precision = 3;
	expect(Math.PI).toBeCloseTo(3.142, precision);
});

it('has some special matchers', () => {
	expect(1).toBeTruthy();
	expect('').toBeFalsy();
	expect().toBeUndefined();
	// .toBeNull(); .toBeDefined(); .toBeNaN();
});

it('gets funky with partial matchers', () => {
	expect(['a', {b: {}}]).toContain({b: {}});
	expect('some string').toContain('some');

	expect({bar: 'baz', ack: 'yaye'}).toEqual(jasmine.objectContaining({bar: 'baz'}));
	expect([1, 2, 3]).not.toEqual(jasmine.arrayContaining([1, 6]));
	expect({foo: 'foobarbaz'}).toEqual({foo: jasmine.stringMatching('baz$')});
});

it('can check for errors', () => {
	const foo = () => {
		throw new TypeError('foo bar baz');
	};

	expect(foo).toThrow();

	// Error message must be exact match in order to pass
	expect(foo).toThrowError('foo bar baz');
	expect(foo).toThrowError(/bar/);
	expect(foo).toThrowError(TypeError);
	expect(foo).toThrowError(TypeError, 'foo bar baz');
});

it('jasmine.any', () => {
	expect({}).toEqual(jasmine.any(Object));
	// Number, Boolean, Date, String, Array
	// Or 'YourType'
});

it('jasmine.anything', () => {
	expect(1).toEqual(jasmine.anything());
});

```

The [matchers source][jasmine-matchers] for if you'd like to take a peek at the implementations.




## Workflow

If you are iterating over the same test(s) you can temporarily run these tests by prefixing
one or more `describe` and/or `it`s with an f.
You can also execute a single file from the CLI `jasmine src/someSpec.js`.

Obviously you'll never want to do the opposite: exclude a test from the suite. But.. If you must, it is possible
to temporarily disable a describe/it by prefixing it with an x.

```js
// f = focused
fdescribe('only my its will run', () => {
	it('which is me', () => {});
	it('and me', () => {});
	xit('but not me', () => {});
});

xit('I will not run, even if the fdescribe above is removed', () => {});
```

Other helpers available in an `it`:
- `pending(string)`. An `it` where the second parameter is omitted is automatically pending.
- `fail(string | Error)`



[jasmine-matchers]: https://github.com/jasmine/jasmine/blob/master/src/core/matchers
