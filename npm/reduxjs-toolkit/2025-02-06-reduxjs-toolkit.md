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

This first post is context and can basically be ignored.  
The remainder of the series are no-nonsense pretty detailed explainations on how to start using
ReduxJS/Toolkit, assuming you're basically already a "legacy" Redux expert but don't want to read the
gazillion pages that is the [default documentation](https://redux.js.org/introduction/getting-started).
{: .notice--info}

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

The application I was migrating was using [`ImmutableJS`](https://github.com/immutable-js/immutable-js) because you know, you want all store state to be immutable, your reducers pure, and it was all the rage back then.
For me though, the cure turned out to be worse than the disease. You could no longer just
do `state.someProp`, you had to do `state.get('someProp')`.

I rather just `...spread` all the way instead.

Immer is the solution the ReduxJS/Toolkit has adopted and I love how it allows you to write reducers
while (almost) not having to think about immutability anymore!

[Part 2: Immer]({% post_url npm/reduxjs-toolkit/2025-02-07-reduxjs-toolkit-immer %})


# createSlice and configureStore

Okay, now that we're Immer experts, let's look at the two most important
exports of the Toolkit: `createSlice` and `configureStore`.

- `createSlice`: each top level object of your state is typically a slice
- `configureStore`: abstracts away the pain that was `createStore`

[Part 3: createSlice and configureStore]({% post_url npm/reduxjs-toolkit/2025-02-08-reduxjs-toolkit-createSlice %})


# createAsyncThunk

On top of the included redux-thunk package, `createAsyncThunk` is the helper function for
all your API call needs.

Coming from "legacy Redux", this is the last basic piece of ReduxJS/Toolkit!

[Part 4: createAsyncThunk]({% post_url npm/reduxjs-toolkit/2025-02-13-reduxjs-toolkit-createAsyncThunk %})


# TypeScript

Keeping everything entirely type-safe.

[Part 5: TypeScript]({% post_url npm/reduxjs-toolkit/2025-02-17-reduxjs-toolkit-typescript %})


# Reselect

More batteries included. You basically only need this when running into
[performance issues]({% post_url npm/react/2023-10-06-react-component-profiling %})
which is like, almost never (if you thought things through). Because JavaScript is
just so damn fast these days.

Still, it's a good idea to just use selectors from the get-go, that way, you will still
not be running into re-rendering performance issues after the addition of 6 years of production data.

[Part 6: createSelector]({% post_url npm/reduxjs-toolkit/2025-02-27-reduxjs-toolkit-createSelector %})


# RTK Query

A much **higher level of abstraction**.  
Okay, we're getting to the meat of ReduxJS/Toolkit now. Yes, only now.

When migrating from classes, plain JavaScript, and all that other stuff that was hot
back in the medieval ages, you may want to do the whole Immer, slices and asyncThunks
stuff; but RTK Query might just be a tad too much in what probably already is a whole
re-write.

There is pretty much an easy 1 to 1 conversion path there. When you want to go to all the way
and also adopt RTK Query, you'll have to change (~~a bit~~) more.

Will it be worth it in the end? Yes. Will it hurt? Also yes.  
So maybe something you want to use for your next NEW React app ;)

<!-- TODO: fix this link!! -->
<!-- [Part 7: createApi](% post_url npm/reduxjs-toolkit/2025-02-19-reduxjs-toolkit-createApi %) -->

Part 7: createApi (under construction!)


# Not Covered

Parts of this series that will probably not be written.


## Other Features

Since I already spent way too much time on this 😀


**Listener Middleware**:

[Listener middleware](https://redux-toolkit.js.org/api/createListenerMiddleware)
  - [Why Listeners for Reactive Logic](https://redux.js.org/usage/side-effects-approaches#why-listeners-for-reactive-logic)


**EntityAdapters**:

The [EntityAdapters](https://redux-toolkit.js.org/api/createEntityAdapter)
are in fact quite handy. There is an example in `moreSlice` in the accompanying code.
If you are doing basic CRUD, this can significantly reduce the boilerplate EVEN MORE.

```ts
import { createEntityAdapter, createSlice } from '@reduxjs/toolkit';

const usersAdapter = createEntityAdapter({
  // In case "id" is not the name of the id property
  selectId: (user: User) => user.name,
  // Sorter for the "All IDs" array
  sortComparer: (a, b) => a.name.localeCompare(b.name),
});

export const usersSlice = createSlice({
  initialState: usersAdapter.getInitialState(),
  // initialState: {
  //   ids: Id[],
  //   entities: Record<Id, T>,
  // }
  reducers: {
    addUser: usersAdapter.addOne,
    addUsers: usersAdapter.addMany,
    updateUser: usersAdapter.updateOne,
    removeUser: usersAdapter.removeOne,
    // ... and many more fns ready for use
  }
});
```


**autoBatchEnhancer**:

[autoBatchEnhancer](https://redux-toolkit.js.org/api/autoBatchEnhancer)
is the built-in (and automatically registered)
mechanic to delay low-priority actions.



## Best Practices

A blog post that has been sitting on my todo list for the longest time is
"_Redux: The Good, The Bad and The Boilerplate_".

ReduxJS/Toolkit proves that you don't need the horror that is your typical
NGRX implementation. Your average enterprise backend developer has to do
something in the frontend and after the initial pants pissing you end up
with a custom selector for EVERYTHING topped with an Effect where the failure
action doesn't do anything (or could be replaced with a single global interceptor)
sprinkled with a few Redux no-nos on top.

Yes, I'm quite frustrated by this boilerplate²².

Aside from the rant, that blog post would be something that explains basic Redux followed by what is
basically the Redux [Style Guide](https://redux.js.org/style-guide/).
