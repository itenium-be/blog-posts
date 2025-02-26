---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit"
subTitle: "The opinionated way of building Redux apps"
date: 2025-02-06
desc: >
  First we went from Flux to Redux. And now to the Toolkit?
  <br>
  It certainly comes with a learning curve... Is it worth
  your time?
bigimg:
  url: reduxjs-toolkit-big.png
  prompt: "toolkit"
  origin: Midjourney
img:
  url: reduxjs-toolkit.png
  origin: ChatGPT
  prompt: "A powerful glowing central hub surrounded by multiple orbiting data streams, symbolizing a single source of truth in application state management. The design is sleek, with futuristic UI elements and a sci-fi aesthetic"
categories: javascript
tags: [tutorial,react,war-story]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "The 'Mythical Zoos' example code and the talk pptx"
package-versions:
  - package: "@reduxjs/toolkit"
    version: 2.5.0
  - package: redux
    version: 5.0.1
  - package: react-redux
    version: 9.2.0
toc:
  title: ReduxJS/Toolkit
  icon: icon-reactjs
socials:
  linkedin: ""
  facebook: ""
  twitter: ""
---

{% include github-stars.html url="reduxjs/redux-toolkit" desc="The official, opinionated, batteries-included toolset for efficient Redux development" %}

We're 2025 and you start a new app with old, trusted and deprecated `create-react-app`,
only to find out that using Redux is also, deprecated.

![Redux createStore deprecation](/assets/blog-images/reduxjs-toolkit-createStore-deprecated.png "Redux createStore deprecation"){: .img-responsive .hide-from-excerpt}

During previous migrations I took a long hard look at the [ReduxJS/Toolkit documentation](https://redux-toolkit.js.org/)
and I was like... Aint nobody got time for that!

For the last migration, I decided to give it go and well, I can already say with conviction,
it is worth the time learning. Reduced boilerplate, batteries included and while the full
documentation is still daunting, you can take it step by step.

<!--more-->

# Immer

{% include github-stars.html url="immerjs/immer" desc="Create the next immutable state by mutating the current one" %}

Before we actually start with ReduxJS/Toolkit (RTK), let's take a step back.

The application I was migrating, was using [`ImmutableJS`](https://github.com/immutable-js/immutable-js) because you know, you want all store state to be immutable, your reducers pure, and it was all the rage back then.
For me though, the cure turned out to be worse than the disease. You could not longer just
do `state.someProp`, you had to do `state.get('someProp')`.

I rather just `...spread` all the way instead.

Immer is the solution the ReduxJS/Toolkit has adopted and I love how it allows you to write reducers
while (almost) not having to think about immutability anymore!

[Part 1: Immer]({% post_url npm/reduxjs-toolkit/2025-02-07-reduxjs-toolkit-immer %})


# createSlice and configureStore

Okay, now that we're Immer experts, let's look at the two most important
exports of the Toolkit: `createSlice` and `configureStore`.

- `createSlice`: each top level object of your state is typically a slice
- `configureStore`: abstracts away the horror that was `createStore`

[Part 2: createSlice and configureStore]({% post_url npm/reduxjs-toolkit/2025-02-08-reduxjs-toolkit-createSlice %})


# createAsyncThunk

On top of the included redux-thunk package, `createAsyncThunk` is the helper function for
all your API call needs.

Coming from "legacy Redux", this is the last basic piece of ReduxJS/Toolkit!

[Part 3: createAsyncThunk]({% post_url npm/reduxjs-toolkit/2025-02-13-reduxjs-toolkit-createAsyncThunk %})


# TypeScript

Keeping everything entirely type-safe.

[Part 4: TypeScript]({% post_url npm/reduxjs-toolkit/2025-02-17-reduxjs-toolkit-typescript %})



# Not yet written

Parts of this series that are not yet written 😀

## Reselect

More batteries included.

Part 5: createSelector

## RTK Query

A much higher level of abstraction.

Part 6: createApi

## And more?

Listener middleware? EntityAdapters?
What is Redux & Redux best practices?
