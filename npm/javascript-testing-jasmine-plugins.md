---
layout: post
title:  "JavaScript Testing: Jasmine plugins"
date:   2017-08-07 00:00:00 +0200
categories: javascript
tags: [javascript,tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/be-pongit/jasmine-tut
---

{% include toc title="Plugins" icon="icon-javascript" %}

What would we be without some extra plugins. There
are over 1000 Jasmine npm packages and we'll cover them all here.


<!--more-->


Or apparently just [`proxyquire`][proxyquire] ;)

Other Jasmine plugins that you might find useful:
- [nock][nock]: Test Http requests
- [rewire][rewire]: Use `rewire('./file.js')` instead of `require()` and get a fresh copy each time


## Proxyquire

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

[nock]: https://github.com/node-nock/nock
[proxyquire]: https://github.com/thlorenz/proxyquire
[rewire]: https://github.com/jhnns/rewire
