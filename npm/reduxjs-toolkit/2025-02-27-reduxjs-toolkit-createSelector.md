---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit: createSelector"
subTitle: "Batteries Included: Reselect"
date: 2025-02-27
desc: >
  Create memoized selector functions to avoid
  nasty re-rendering!
bigimg:
  url: reduxjs-toolkit-reselect-big.png
  prompt: "A fun, cartoonish librarian racing through endless bookshelves, quickly picking and sorting books into the right sections with incredible precision. Some books glow as they are selected, while others stay on the shelves."
  origin: Midjourney
img:
  url: reduxjs-toolkit-reselect.png
  origin: ChatGPT
  prompt: "A futuristic energy core at the center of a massive digital network, precisely extracting and refining structured data from chaotic streams of information. Glowing, transparent data streams flow into the core, where a high-tech filtration system refines and optimizes them before channeling the clean output into structured pathways. The scene has a cyberpunk aesthetic, with neon-lit circuits and a sleek, high-tech control center."
categories: javascript
tags: [tutorial,react]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "Check todoSelectors.ts to see the examples in action"
package-versions:
  - package: reselect
    version: 5.1.1
toc:
  title: reselect
  icon: icon-reactjs
socials:
  linkedin: ""
  facebook: ""
  twitter: ""
---

{% include github-stars.html url="reduxjs/reselect" desc="Create memoized 'selector' functions" %}

One of the Redux Style Guide "Strongly Recommended" rules is
[Keep State Minimal and Derive Additional Values](https://redux.js.org/style-guide/#keep-state-minimal-and-derive-additional-values):

Keep the actual data in the Redux store as minimal as possible, and derive additional values from that state as needed.
{: .notice--info}

Keeping all that efficient is where `createSelector` comes into play.

<!--more-->

# Examples

By convention, selectors are named [`selectThing`](https://redux.js.org/style-guide/#name-selector-functions-as-selectthing):

- `inputSelectors`: The first argument, the dependencies, is an array of root state selectors
- `combiner`: The second argument, the result function, calculates the derived state
- `createSelectorOptions`: The third (optional) options argument configures how the selector behaves

```ts
import { createSelector } from "@reduxjs/toolkit";

const selectMyTodosCompletedCount = createSelector(
  [
    (state: RootState) => state.todos,
    (state: RootState) => state.user.id,
  ],
  (todos, userId) => {
    return todos.filter(todo => todo.userId === userId && todo.done).length;
  }
)


// Usage in a component
import { useSelector } from "react-redux";

const TodosDoneCount = () => {
  const doneCount = useSelector(selectMyTodosCompletedCount);
  return <>✔️ {doneCount}</>;
}
```

Every time the root state changes, it will execute the inputSelectors; in this case
`state.todos` and `state.user.id`. If the returned references have not changed, the memoized
count is returned, otherwise the combiner is executed.

This is also why you want to select the todos and the userId separately, if you select them
as `({todos: state.todos, userId: state.user.id})`, you'd get a new reference every time,
effectively rendering the selector useless!

The Reselect development-only [`inputStabilityCheck`](https://reselect.js.org/api/development-only-stability-checks#inputstabilitycheck)
executes the selector twice and logs a warning to the console
if one of the inputSelectors returns a different reference.


## With Parameters

Filter the todo list with a needle parameter.

```ts
const selectTodos = createSelector(
  [
    (state: RootState) => state.todos,
    (_, needle: string) => needle,
  ],
  (todos, needle) => todos.filter(todo => todo.text.includes(needle))
)

// Usage in a component
const TodoList = ({searchNeedle}) => {
  const todos = useSelector(state => selectTodos(state, searchNeedle));
  return todos.map(todo => (
    <Todo key={todo.id} todo={todo} />
  );
}
```

If you don't like this syntax, see the [Reselect FAQ](https://reselect.js.org/FAQ#how-can-i-make-a-curried-selector)
on how to create a curried selector instead.

```ts
// This would allow you to do this instead:
const todos = useSelector(selectTodos(searchNeedle));

// The FAQ also contains a recipe to turn this into:
const todos = useSelectTodos(searchNeedle);
```


### Multiple Parameters

```ts
const selectTodos = createSelector(
  [
    (state: RootState) => state.todos,
    (_, needle: string) => needle,
    (_, __, caseSensitive: boolean) => caseSensitive
  ],
  (todos, needle, caseSensitive) => { /* magic here */ }
)
```


## createSelectorOptions

Reselect will only execute the combiner fn when the inputs have changed,
which is typically your entire root state (with potentially additional parameters)
AND the result of those input selectors has changed.

![How reselect works](/assets/blog-images/reduxjs-toolkit-reselect-how-it-works.png "How reselect works"){: .img-responsive}

Configure this behavior with `argsMemoize(Options)` and `memoize(Options)`.

If you want to change the default behavior of many/all
selectors, use [createSelectorCreator](https://reselect.js.org/api/createSelectorCreator).

### lruMemoize

[`lruMemoize`](https://reselect.js.org/api/lruMemoize)
(Least Recently Used) was the default before v5 of Reselect.
It had a default cache size of 1, meaning a recomputation every time
one of the inputSelectors changed.

The "problem" with lruMemoize was that a cache of size 1 might
not be that useful! This was typically solved with `useMemo`
or by passing `memoizeOptions: { maxSize: 5 }`.

```ts
import { lruMemoize } from "reselect";
import { shallowEqual } from "react-redux";

export const selectMyTodosCompletedCount = createSelector(
  [(state: RootState) => ({todos: state.todos, userId: state.more.users.ids[0]})],
  ({todos, userId}) =>  todos.filter(todo => todo.userId === userId && todo.done).length,
  {
    memoize: lruMemoize,
    memoizeOptions: {
      equalityCheck: (a, b) => a.todos === b.todos && a.userId === b.userId,
      // resultEqualityCheck: shallowEqual,
      // maxSize: 10
    },
    // argsMemoize: lruMemoize,
    // argsMemoizeOptions: {
    //   equalityCheck: shallowEqual,
    //   resultEqualityCheck: shallowEqual,
    //   maxSize: 10
    // }
  }
)
```

### weakMapMemoize (default)

[`weakMapMemoize`](https://reselect.js.org/api/weakMapMemoize)
is the new default since v5 and provides a dynamic cache size
out of the box.


### unstable_autotrackMemoize (experimental)

There is also the experimental [`unstable_autotrackMemoize`](https://reselect.js.org/api/unstable_autotrackMemoize),
which, like `proxy-memoize` below, can be more efficient.

```ts
import { unstable_autotrackMemoize } from "reselect";

// The combiner (todos.filter) will NOT execute when we
// toggle a Todo between done/not done as that field is
// not accessed.
const selectTodos = createSelector(
  [
    (state: RootState) => state.todos,
    (_, needle: string) => needle,
  ],
  (todos, needle) => todos.filter(todo => todo.text.includes(needle)),
  {
    memoize: unstable_autotrackMemoize,
  }
)
```


# createSlice

Common selectors can already be defined in the `createSlice` setup.

```ts
const todoSlice = createSlice({
  initialState: [],

  // Predefined selectors, which receive the slice's state as their first parameter.
  selectors: {
    selectTodo: (state, id: number): Todo | undefined => {
      return state.find(todo => todo.id === id);
    },
  },
});

export const { selectTodo } = todoSlice.selectors;

// Usage in a component
const todo = useSelector(state => selectTodo(state, todoId));
```



# TypeScript

See [ReduxJS/Toolkit: TypeScript]({% post_url npm/reduxjs-toolkit/2025-02-17-reduxjs-toolkit-typescript %})
if you're unsure how to create the `RootState`.

```ts
import { createSelector } from "@reduxjs/toolkit";
const createAppSelector = createSelector.withTypes<RootState>();
```

# Debugging

{% include github-stars.html url="skortchmark9/reselect-tools" desc="Debugging Tools for Reselect" %}

Once you get many selectors depending on other selectors, you can use
this tool to visualize re-computations.

If your debugging needs are basic, you can check the
[Output Selector Fields](https://reselect.js.org/api/createSelector#output-selector-fields):

```ts
const meta = {
  lastResult: selectMyTodosCompletedCount.lastResult(),
  recomputations: selectMyTodosCompletedCount.recomputations(),
  dependencyRecomputations: selectMyTodosCompletedCount.dependencyRecomputations(),
}
```


# Alternatives

## proxy-memoize

{% include github-stars.html url="dai-shi/proxy-memoize" desc="Intuitive magical memoization library with Proxy and WeakMap" %}

This will re-execute when the `done` property toggles even though
it has no impact on the actual result.

```ts
import { createSelector } from "reselect";

const selectTodoDescriptionsReselect = createSelector(
  [state => state.todos],
  todos => todos.map(todo => todo.text)
)
```

With `proxy-memoize`, this would not be the case as due to the proxy,
it figures only text is actually relevant for selector.

```ts
import { memoize } from "proxy-memoize";

const selectTodoDescriptionsProxy = memoize(state =>
  state.todos.map(todo => todo.text)
)
```

For this reason **the ReduxJS/Toolkit officially encourages you to consider using proxy-memoize as a viable alternative to Reselect.**



## re-reselect

{% include github-stars.html url="toomuchdesign/re-reselect" desc="Enhance Reselect selectors with deeper memoization and cache management." %}

As of Reselect v5, what you'd need this library for can now achieved with Reselect `createSelectorOptions` natively.
