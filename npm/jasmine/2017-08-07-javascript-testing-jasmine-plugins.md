---
layout: post
author: Wouter Van Schandevijl
title:  "JavaScript Testing: Jasmine plugins"
date:   2017-08-07 00:00:00 +0200
img: javascript-testing-jasmine-customization.png
desc: >
  What would we be without some extra plugins. There
  are over 1000 Jasmine npm packages and we'll cover them all here.
categories: javascript
tags: [tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/itenium-be/jasmine-tut
toc:
    title: Plugins
    icon: icon-javascript
lastUpdate: 2019-01-17 00:00:00 +0200
updates:
  - date: 2019-01-17 00:00:00 +0200
    desc: Added reporter plugins
---

What would we be without some extra plugins. There
are over 1000 Jasmine npm packages and we'll cover them all here.


<!--more-->



Or apparently just
{% include github-stars.html url="thlorenz/proxyquire" %}

<br>
and some custom reporters:  
{% include github-stars.html url="onury/jasmine-console-reporter" %}
{% include github-stars.html url="bcaudan/jasmine-spec-reporter" %}

<br>
Other Jasmine plugins you might find useful:

{% include github-stars.html url="node-nock/nock" desc="Test Http requests" %}
{% include github-stars.html url="jhnns/rewire" desc="Use `rewire('./file.js')` instead of `require()` and get a fresh copy each time" %}


# Jasmine Console Reporter

**Prettier output**
```
npm install jasmine-console-reporter --save-dev
```

Create a `spec/helpers/reporter.js` file with the following content:
```
const JasmineConsoleReporter = require('jasmine-console-reporter');
const myReporter = new JasmineConsoleReporter({
  colors: 1,           // (0|false)|(1|true)|2
  cleanStack: 1,       // (0|false)|(1|true)|2|3
  verbosity: 4,        // (0|false)|1|2|(3|true)|4
  listStyle: 'indent', // "flat"|"indent"
  timeUnit: 'ms',      // "ms"|"ns"|"s"
  timeThreshold: { ok: 500, warn: 1000, ouch: 3000 }, // Object|Number
  activity: false,     // boolean or string ("dots"|"star"|"flip"|"bouncingBar"|...)
  emoji: true,
  beep: true,
});
jasmine.getEnv().clearReporters();
jasmine.getEnv().addReporter(myReporter);
```

## Jasmine Spec Reporter

Alternatively, there is also [jasmine-spec-reporter][jasmine-spec-reporter]:  
```
npm install jasmine-spec-reporter --save-dev
```

Usage:  
```js
const SpecReporter = require('jasmine-spec-reporter').SpecReporter;

jasmine.getEnv().clearReporters();
jasmine.getEnv().addReporter(new SpecReporter({
  spec: {
    displayPending: true
  }
}));
```

# Proxyquire

Stub imports of the code being tested.

```
npm install --save-dev proxyquire
```

**api.js**:
```
module.exports = {
  someLongRunningOperation: function() {
    for (var i = 0; i <= 1000; i++) {}
  }
};
```

**cut.js**:
```js
const webApi = require('./api.js');
module.exports = function() {
  webApi.someLongRunningOperation();
};
```

**spec.js**:
```js
const proxyquire = require('proxyquire');
var apiStub = {};
const cut = proxyquire('./cut.js', {'./api.js': apiStub});

describe('fake the api call', function() {
  beforeEach(function() {
    this.hasRunStub = false;
    apiStub.someLongRunningOperation = () => this.hasRunStub = true;
  });

  it('should not actually call the api', function() {
    cut();
    expect(this.hasRunStub).toBe(true);
  });
});
```

[jasmine-spec-reporter]: https://github.com/bcaudan/jasmine-spec-reporter
