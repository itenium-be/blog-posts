---
layout: post
author: Wouter Van Schandevijl
title:  "Google I/O '19: WebAssembly for Web Developers"
date:   2019-05-19 00:00:00
desc: >
  Google I/O '19 talk on why you would ever need WebAssembly.
bigimg:
  url: webassembly-big.png
  desc: Photo by Vlad Tchompalov
  origin: https://unsplash.com/photos/KHxxCc8XMNE
img:
  url: webassembly.png
  desc: Photo by Vadim Sherbakov
  origin: https://unsplash.com/photos/osSryggkso4
categories: javascript
tags: [tech-talk]
extras:
  - githubproject: https://github.com/itenium-be/TechTalk-WebAssembly
    githubtext: "HelloWorld Emscripten example"
interesting:
  - git: AssemblyScript/assemblyscript
    desc: "Definitely not a TypeScript to WebAssembly compiler"
  - git: WebAssembly/proposals
    desc: "Tracking WebAssembly proposals"
  - url: https://www.youtube.com/playlist?list=PLNYkxOF6rcIATmAmz7HcCzongGvQEtx8i
    desc: "More sessions from Google I/O '19"
toc:
  title: WebAssembly
  icon: icon-javascript
---

{% include post/youtube.html id="njt-Qzw0mVY" desc="" %}

<!--more-->


# Emscripten

{% include github-stars.html url="emscripten-core/emscripten" desc="An LLVM-to-Web Compiler" %}


Brings your existing C/C++ applications to the web (ex: Autocad, Game Engines like Unity).
Provides a drop-in replacement: Whatever you wrote, it should just work on the web.
It emulates OpenGL, the FileSystem etc.


# For Web Developers

Two main use cases
- Surgical replacement of tiny modules in your JavaScript, the hot paths, the bottlenecks, with WebAssembly
- Use existing libraries that do not exist on npm from another ecosystem (C/C++, Rust, ...)


# The Performance Myth

WebAssembly and JavaScript both have the same peak performance. But...
JavaScript can easily fall off the "fast path" while WebAssembly will not.

{% include post/image.html file="webassembly-v8-js-vs-wasm.png" desc="Inside V8" maxWidth="700px" %}

- `js` input
    - Ignition: Interpretes the JS text and runs it. While running, collects analytics data about how the code is behaving before TurboFan kicks in.
    - TurboFan: The optimizing compiler.
- `wasm` input
    - Liftoff: The streaming WebAssembly compiler which generates machine code.
    - TurboFan: The optimizing compiler which generates optimized code.


With JavaScript input, TurboFan will deopt (deoptimization) when
the assumptions from the collected analytics no longer hold true
reverting back to the Ignition interpreter.
With Liftoff you always stay on the fast path.

**WebAssembly delivers more predictable performance than JavaScript**

{% include post/image.html file="webassembly-js-vs-wasm.png" desc="The peak performance is about the same but in some browsers the JavaScript performance is way off!" maxWidth="678px" %}


# The Future

#### WebAssembly Threads and SIMD

Will allow WebAssembly to outperform JavaScript.

- Shared Linear Memory
- Atomic operations
- Implemented with WebWorkers
- Ships with Chrome 74


#### Host bindings

<!-- IDL: Interface Description Language -->
Interact easily with JavaScript and DOM objects with the **Web IDL interface**.
Will also greatly reduce the glue overhead that is currently required.

Web IDL is a requirement for other proposals such as **Garbage Collection**,
**Tail Call optimizations** and **Exception Handling**.


#### ECMA module integration

```javascript
import {foo} from './myModule.wasm';
```



# An Example

## Windows Emscripten SDK Installation

[Install the Emscripten SDK](https://emscripten.org/docs/getting_started/downloads.html)

```powershell
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
.\emsdk.ps1 install latest
.\emsdk.ps1 activate latest
emsdk_env.bat
```

## An Application

Your original, mega complex C application `HelloWorld.c`  
```c
#include <stdio.h>

int main(int argc, char ** argv) {
    printf("Hello World\n");
}
```

Turning it into a `wasm` file:  
```bash
emcc HelloWorld.c -s WASM=1 -o HelloWorld.html
npx http-server
```

Open the generated `HelloWorld.html` in a browser to see your WebAssembly file executed!

For this "program", a 40kb `HelloWorld.wasm` file
and 55kb of JavaScript glue in `HelloWorld.js` were generated.
