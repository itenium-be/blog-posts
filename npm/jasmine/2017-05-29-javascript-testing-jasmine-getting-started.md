---
layout: post
author: Wouter Van Schandevijl
title:  "JavaScript Testing: Getting started with Jasmine"
date:   2017-05-29 12:00:00 +0200
categories: javascript
tags: [tutorial,testing]
img:
  url: jasmine.jpg
  desc: Photo by Helena Munoz
  origin: https://unsplash.com/photos/WbiDCxOnytg
series: js-testing
extras:
  - githubproject: https://github.com/itenium-be/jasmine-tut
interesting:
  - url: https://jasmine.github.io/api/edge/matchers.html
    desc: "Jasmine documentation: Matchers"
  - git: jasmine/jasmine
toc:
  title: Getting Started
  icon: icon-javascript
last_modified_at: 2023-03-28 00:00:00 +0200
updates:
  - date: 2023-03-28 05:00:00 +0200
    desc: jasmine-ts is deprecated. Updated to Jasmine 4.3.0.
package-versions:
  - package: jasmine
    version: 4.3.0
---

Probably the most widely used JavaScript testing framework.

This tutorial covers installation, configuration and execution only.
For the Jasmine syntax, see parts 2 to 4!

<!--more-->

# Install

Add Jasmine to your project:

```sh
npm install --save-dev jasmine
./node_modules/.bin/jasmine init
```

You can omit the `./node_modules/.bin/` if you have Jasmine installed globally.
The following code snippets will assume you did.

If you have no clue how to start, some **working examples** can be created with `jasmine examples`.



# Configure

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



# Run

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


# Transpile

## Babel

Node can handle most syntax you throw at it, but if you do use
something too fancy, [Babel][babel-setup] to the rescue!

```sh
npm install --save-dev @babel/cli @babel/core @babel/node

# Add all the presets/plugins you're using
npm install --save-dev @babel/preset-env @babel/polyfill core-js
```

Add all the presets/plugins to your `.babelrc`
```json
{
  "presets": ["@babel/preset-env"]
}
```

File `run-jasmine.js`:  

```js
import '@babel/polyfill';
import 'core-js/shim';
import Jasmine from 'jasmine';

const jasmine = new Jasmine();
jasmine.loadConfigFile('./spec/support/jasmine.json');
jasmine.execute();
```

The accompanying code has a working example for Babel transpilation in the `babel` folder.

### Building and watching

`package.json` scripts:  

```json
"scripts": {
  // Clean generated files (requires npm i -D del-cli)
  "babel:clean": "del-cli dist",

  // Transpile to dist directory
  "babel:build": "babel babel --out-dir dist --ignore some-generated-dir/",

  // Clean, build and run jasmine
  "babel": "npm run babel:clean && npm run babel:build && node babel/dist/run-jasmine.js",

  // Watch for changes
  "babel:w": "nodemon --exec \"npm run babel\" --ignore babel/dist/"
}
```



## TypeScript


`ts-node` to the rescue!  
Make sure you change your `jasmine.json` file extensions to `.ts`.

```sh
npm install --save-dev jasmine @types/jasmine typescript ts-node cross-env
```

`jasmine.json` content:  

```json
{
  "spec_dir": "spec",
  "spec_files": [
    "**/*[sS]pec.ts"
  ],
  "helpers": [
    "helpers/**/*.ts"
  ],
  "stopSpecOnExpectationFailure": false,
  "random": true,
  "reporters": [
    {
      "name": "jasmine-spec-reporter#SpecReporter",
      "options": {
        "displayStacktrace": "all"
      }
    }
  ]
}
```


`package.json` scripts:  

```json
"scripts": {
  "test": "cross-env NODE_OPTIONS=--loader=ts-node/esm jasmine",

  // -e ts: reload on file extension ts
  "test:w": "nodemon -e ts --exec \"npm test\""
}
```


[babel-setup]: http://babeljs.io/docs/setup
