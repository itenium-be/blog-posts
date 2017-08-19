---
layout: post
title:  "JavaScript Testing: Getting started with Jasmine"
date:   2017-05-29 12:00:00 +0200
categories: javascript
tags: [javascript,tutorial,testing]
series: js-testing
extras:
  - githubproject: https://github.com/be-pongit/jasmine-tut
---

{% include toc title="Getting Started" icon="icon-javascript" %}

Probably the most widely used JavaScript testing framework.

This tutorial covers installation, configuration and execution only.
For the Jasmine syntax, see parts 2 to 4!

<!--more-->

## Install

Add Jasmine to your project:

```sh
npm install --save-dev jasmine
./node_modules/.bin/jasmine init
```

You can omit the `./node_modules/.bin/` if you have Jasmine installed globally.
The following code snippets will assume you did.

If you have no clue how to start, some **working examples** can be created with `jasmine examples`.



## Configure

`jasmine init` creates `./spec/support/jasmine.json` where you can configure your testing preferences.
If you want to put the jasmine.json file somewhere else, you can do so 
by providing a value for the environment variable `JASMINE_CONFIG_PATH`.

```json
{
	"spec_dir": "spec",
	"spec_files": [
		"**/*[sS]pec.js"
	],
	"helpers": [
		"helpers/**/*.js"
	]
}
```

The default Jasmine configuration makes the assumption that 
all tests are placed in `./spec/` and are suffixed with `spec.js`.
Helpers (not covered here) allow you to provide custom matchers in your tests.

If you want to place your tests closer to the actual code they are testing, you can
simply change `spec_dir`. If the source code is in `./src` use the following spec_dir values:
- `src/**/spec`: The tests for `/src/path/to/file.js` would be in `/src/path/to/spec/fileSpec.js`.
- `src`: To place the tests in the same folder as the source files.

Note that paths in the json are relative to where `jasmine init` was executed.



## Run

Run the tests with a simple `jasmine` or add to your package.json:

```json
"scripts": {
	"test": "jasmine",
	"test:w": "nodemon --exec \"npm test\"",
	"test:alt": "jasmine JASMINE_CONFIG_PATH=spec/support/jasmine-alt.json",
	"test:cli": "jasmine --filter=\"describe and/or it\" --stop-on-failure=true --no-color --random=true -seed=7337"
},
```

And start once with `npm test` or just `npm t`. Or start watching with `npm run test:w`.

`test:w`(atch) requires a `npm install --save-dev nodemon` to work.

`test:alt` and `test:cli` demonstrate the available Jasmine CLI parameters.


## Run ES2015+

As soon as one of your tests hits `import x from` it's a crash. Oh noes!
Chances are you are already using Babel for older browser support.
The very same [Babel][babel-setup] to the rescue here!

```sh
npm install --save-dev babel-register

# Add all the presets/plugins you're using
npm install --save-dev babel-preset-es2015
```

Add all the presets/plugins to your `.babelrc`
```json
{
	"presets": ["es2015"]
}
```

And add a `package.json` script: `babel-node run.js`
```js
// run.js
import Jasmine from 'jasmine';

const jasmine = new Jasmine();
jasmine.loadConfigFile('./spec/support/jasmine.json');
jasmine.execute();
```



[babel-setup]: http://babeljs.io/docs/setup
