---
layout: post
author: Wouter Van Schandevijl
title:  "JavaScript Testing: Jasmine plugins"
date:   2017-08-07 00:00:00 +0200
img:
  url: jasmine2.jpg
  desc: Photo by Aliis Sinisalu
  origin: https://unsplash.com/photos/yOh9lWBDjP4
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
last_modified_at: 2019-01-17 00:00:00 +0200
updates:
  - date: 2023-03-28 00:00:00 +0200
    desc: "Colorblindness: added color configuration + added jasmine-reporters"
  - date: 2019-01-17 00:00:00 +0200
    desc: Added reporter plugins
package-versions:
  - package: jasmine
    version: 4.3.0
  - package: proxyquire
    version: 2.1.3
---

What would we be without some extra plugins. There
are over 1000 Jasmine npm packages and we'll cover them all here.


<!--more-->



Or apparently just
{% include github-stars.html url="thlorenz/proxyquire" desc="Proxies nodejs require in order to allow overriding dependencies during testing." %}

<br>
and some custom reporters:  
{% include github-stars.html url="bcaudan/jasmine-spec-reporter" desc="Real time console spec reporter for jasmine testing framework" %}
{% include github-stars.html url="Marak/colors.js" desc="Color configuration for jasmine-spec-reporter" %}
{% include github-stars.html url="larrymyers/jasmine-reporters" desc="Reporter classes for the jasmine test framework." %}


<br>
Other Jasmine plugins you might find useful:

{% include github-stars.html url="node-nock/nock" desc="HTTP server mocking and expectations library for Node.js" %}
{% include github-stars.html url="visionmedia/supertest" desc="Super-agent driven library for testing node.js HTTP servers using a fluent API." %}
{% include github-stars.html url="jhnns/rewire" desc="Use `rewire('./file.js')` instead of `require()` and get a fresh copy each time" %}

# Reporters

## Jasmine Spec Reporter

Probably your goto console logger: [jasmine-spec-reporter][jasmine-spec-reporter]:  


```sh
npm install jasmine-spec-reporter --save-dev
```

Usage:  

```js
const SpecReporter = require('jasmine-spec-reporter').SpecReporter;

jasmine.getEnv().clearReporters();
jasmine.getEnv().addReporter(new SpecReporter({
  spec: {
    displayPending: true,
    // See configuration.d.ts for more config
    // Colors: https://github.com/Marak/colors.js
    colors: {
        failed: 'magenta',
        prettyStacktraceError: 'magenta',
    }
  }
}));
```

## Jasmine-Reporters

A collection of reporters: JUnitXmlReporter, NUnitXmlReporter,
AppVeyor, TapReporter, TeamCityReporter and TerminalReporter.


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


# VSCode Integration

The Jest VSCode test runner got me thinking, this probably also exists for Jasmine.

{% include github-stars.html url="hbenl/vscode-jasmine-test-adapter" desc="Jasmine Test Adapter for the VS Code Test Explorer" %}

And [instructions for setting up a `launch.json` on Stackoverflow](https://stackoverflow.com/questions/45036291/how-to-run-jasmine-tests-in-visual-studio-code)



[jasmine-spec-reporter]: https://github.com/bcaudan/jasmine-spec-reporter
