---
layout: post
draft: true
title:  "JavaScript Testing: Jasmine plugins"
date:   3017-08-08 00:00:00 +0200
categories: javascript
tags: [javascript,tutorial,testing,react]
series: js-testing
extras:
  - githubproject: https://github.com/be-pongit/jasmine-tut
---

{% include toc title="Jest" icon="icon-javascript" %}

Jest has pretty reporting which Jasmine by itself is lacking.
Even without actually using React.

<!--more-->


The [migration][migration] guide didn't quite succeed in converting the tests we've written so far.
Even `BasicExample.js` failed :)

```
npm install -g jest-codemods
jest-codemods
```
jest-codemods provides a nice CLI experience but don't be optimistic about the results.


https://github.com/facebook/jest && https://github.com/airbnb/enzyme/



## Karma

```
npm install karma karma-jasmine --save-dev
```


http://karma-runner.github.io/1.0/intro/installation.html
https://github.com/karma-runner/karma


https://jasmine.github.io/2.1/custom_reporter.html

```js
// actually include a reporter here
```

Integration with your favourite CI: https://github.com/larrymyers/jasmine-reporters

TODO: really need to check the output for: https://github.com/mhevery/jasmine-node

https://github.com/onury/jasmine-console-reporter

helpers.js
```js
var JasmineConsoleReporter = require('jasmine-console-reporter');
var myReporter = new JasmineConsoleReporter({
    colors: 1,           // (0|false)|(1|true)|2
    cleanStack: 1,       // (0|false)|(1|true)|2|3
    verbosity: 4,        // (0|false)|1|2|(3|true)|4
    listStyle: 'indent', // "flat"|"indent"
    activity: false
});
jasmine.getEnv().addReporter(myReporter);
```





[migration]: https://facebook.github.io/jest/docs/en/migration-guide.html
