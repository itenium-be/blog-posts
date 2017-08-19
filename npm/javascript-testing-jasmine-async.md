---
layout: post
title:  "JavaScript Testing: Jasmine async syntax"
date:   2017-05-31 12:00:00 +0200
categories: javascript
tags: [javascript,tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/be-pongit/jasmine-tut
---

{% include toc title="Jasmine Async" icon="icon-javascript" %}

Need to test async code? No problem for Jasmine. 
There is `done()` to inform Jasmine a test has finished running.

With `jasmine.clock()`, the value of `new Date()` can be manipulated.

Finally instructions on how to configure Babel for async/await in tests.

<!--more-->


## Example

`it`, `beforeEach`, `afterAll`, ... have an additional optional parameter called timeout (ms).
A test fails for such a block when nor `done()` nor `done.fail()` is called within 
`jasmine.DEFAULT_TIMEOUT_INTERVAL` which defaults to 5000 (ms).

```js
beforeAll(done => {
	// The same construct can be used for
	// beforeEach, afterEach and afterAll
	done();
}, customTimeout);

it('has an optional "done" parameter', done => {
	Promise.resolve()
		.then(() => expect(true).toBeTruthy())
		.then(done)
		.catch(done.fail);
});

xit('can fail with a specific message', done => {
	Promise.reject().catch(done.fail.bind(this, 'done.fail("with your error message")'));
}, customTimeout);
```


## Time Travel

```js
beforeEach(() => jasmine.clock().install());

it("doesn't have to take the slow path", () => {
	var timePassed = 0;
	setTimeout(() => {
		timePassed += 100;
	}, 100);

	expect(timePassed).toBe(0);
	jasmine.clock().tick(100);
	expect(timePassed).toBe(100);
});


it('can hijack new Date', () => {
	const baseTime = new Date(2013, 9, 23);
	jasmine.clock().mockDate(baseTime);

	jasmine.clock().tick(50);
	expect(new Date().getTime()).toEqual(baseTime.getTime() + 50);
});


afterEach(() => jasmine.clock().uninstall());
```


## Async Await

For the really hip people, and with a little help of `.babelrc`, you can write your tests like:
```js
it("'s async/await, it's so 2017!", done => {
	(async () => {
		const pie = await Promise.resolve(3.14);
		expect(pie).toBeCloseTo(3, 0);
		done();
	})().catch(done.fail);
});
```

You'll need these Babel plugins:
```
npm i -D babel-plugin-syntax-async-functions
npm i -D babel-plugin-transform-async-to-generator

// .babelrc:
plugins: ["syntax-async-functions", "transform-async-to-generator"]
```

The accompanying code has a working example for async/await in the `es2015` folder.
