---
layout: post
author: Wouter Van Schandevijl
title:  "JavaScript Testing: Jasmine async syntax"
desc: Test asynchronous code with Jasmine. Includes instructions on how to use ES2017 async/await.
img:
  url: jasmine3.jpg
  desc: Photo by Irina Iriser
  origin: https://unsplash.com/photos/vB4_CtsfaZ0
date:   2017-05-31 12:00:00 +0200
categories: javascript
tags: [tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/itenium-be/jasmine-tut
toc:
  title: Jasmine Async
  icon: icon-javascript
last_modified_at: 2019-01-18 00:00:00 +0200
updates:
  - date: 2023-03-28 05:00:00 +0200
    desc: Updated to Jasmine 4.3.0.
  - date: 2019-01-18 00:00:00 +0200
    desc: await/async is pretty standard now. Added expectAsync examples.
package-versions:
  - package: jasmine
    version: 4.3.0
---

Need to test async code? No problem for Jasmine. 
There is `done()` to inform Jasmine a test has finished running.

With `jasmine.clock()`, the value of `new Date()` can be manipulated.

<!--more-->


# Example

`it`, `beforeEach`, `afterAll`, ... have an additional optional parameter called timeout (ms).
A test fails for such a block when nor `done()` nor `done.fail()` is called within 
`jasmine.DEFAULT_TIMEOUT_INTERVAL` which defaults to 5000 (ms).

```js
it('can use async/await', async () => {
  const pie = await Promise.resolve(3.14);
  expect(pie).toBe(3.14);
});


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


# Time Travel

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

# expectAsync

```js
it('can check for resolved', async () => {
  const pie = Promise.resolve(3.14);
  await expectAsync(pie).toBeResolved();
  return expectAsync(pie).toBeResolvedTo(3.14);
});

it('can check for rejected', async () => {
  const pie = Promise.reject('reasons');
  await expectAsync(pie).toBeRejected();
  await expectAsync(pie).toBeRejectedWith('reasons');
});

it('can pass context - which will show on failure', async () => {
  const pie = Promise.resolve(42);
  await expectAsync(pie).withContext('test').toBeResolved();
});
```
