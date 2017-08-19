---
layout: post
title:  "JavaScript Testing: Jasmine Spies"
date:   2017-05-30 15:00:00 +0200
categories: javascript
tags: [javascript,tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/be-pongit/jasmine-tut
---

{% include toc title="Jasmine Spies" icon="icon-javascript" %}

Spies, the Jasmine implementation for mocks
featuring `spyOn` and the new `spyOnProperty` aswell as `jasmine.createSpy(Obj)`
and how to inspect calls made.

<!--more-->



## spyOn

```js
var bar = 0;
const foo = {
	setBar(value, times) {
		bar = value * (times || 1);
		return bar;
	}
};

beforeEach(() => {
	const theSpy = spyOn(foo, 'setBar');
	expect(theSpy.and.identity()).toBe('setBar');

	foo.setBar(1);
	foo.setBar(2, 10);
});

it('can check if called', () => {
	expect(foo.setBar).toHaveBeenCalled();
	expect(foo.setBar.calls.any()).toBe(true);
});

it('how many times', () => {
	expect(foo.setBar).toHaveBeenCalledTimes(2);
	expect(foo.setBar.calls.count()).toBe(2);
});

it('and with which parameters', () => {
	expect(foo.setBar).toHaveBeenCalledWith(1);
	expect(foo.setBar).toHaveBeenCalledWith(2, 10);
});

// Also:
// toHaveBeenCalledBefore(anotherSpy)

it('but does not execute the function', () => {
	expect(bar).toBe(0);
});
```

Or create your own spies:
```
it('create bare spy without implementation', () => {
	const spy = jasmine.createSpy().and.returnValue(true);
	expect(spy()).toBe(true);
});

it('create spy object with spy functions action1 and action2', () => {
	const spyObj = jasmine.createSpyObj('id', ['action1', 'action2']);
	spyObj.action1();
	expect(spyObj.action1).toHaveBeenCalled();
	expect(spyObj.action2).not.toHaveBeenCalled();
});

it('create spy object with return values 15 and 18', () => {
	const spyObj = jasmine.createSpyObj('id', {action1: 15, action2: 18});
	expect(spyObj.action1()).toBe(15);
});
```


## Spy.and

```js
beforeEach(() => {
	this.foo = foo;
});

it('can actually execute the function', () => {
	spyOn(this.foo, 'setBar').and.callThrough();
	foo.setBar(2, 10);
	expect(bar).toBe(2 * 10);
});

it('or do something else', () => {
	spyOn(this.foo, 'setBar').and.callFake(value => value + 1);
	const calced = foo.setBar(5);
	expect(calced).toBe(6);
});

it('allows returning canned values', () => {
	spyOn(this.foo, 'setBar').and.returnValue(1);
	//.and.returnValues('first call result', 'second');
	const canned = foo.setBar();
	expect(canned).toBe(1);
});

it('or just throw an error', () => {
	spyOn(this.foo, 'setBar').and.throwError('quux');
	expect(foo.setBar).toThrowError('quux');
});
```



## Spy.calls

```js
it('knows about arguments, returnValues and more', () => {
	const spie = jasmine.createSpy('identity').and.callFake(v => v * 5);
	spie(1);
	spie(2, 'arg');

	// spie.calls.any() and spie.calls.count()

	// Full callData
	expect(spie.calls.first()).toBe(spie.calls.all()[0]);
	expect(spie.calls.mostRecent()).toEqual({
		object: jasmine.any(Object),
		args: [2, 'arg'],
		returnValue: 10,
		invocationOrder: jasmine.any(Number)
	});

	// Just the arguments
	expect(spie.calls.argsFor(0)).toEqual([1]);
	expect(spie.calls.allArgs()).toEqual([[1], [2, 'arg']]);

	spie.calls.reset();
	expect(spie).not.toHaveBeenCalled();
});
```



## spyOnProperty

```js
const foop = {
	get value() {},
	set value(v) {}
};

it('can spy on getter', () => {
	spyOnProperty(foop, 'value', 'get').and.returnValue(1);
	expect(foop.value).toBe(1);
});

it('and on setters', () => {
	const spiez = spyOnProperty(foop, 'value', 'set');
	foop.value = true;
	expect(spiez).toHaveBeenCalled();
});
```
