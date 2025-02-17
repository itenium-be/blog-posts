---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit: Immer"
subTitle: "Seamless immutability with immer"
date: 2025-02-07
desc: >
  Write pure reducers without the spread hell.
  <br>
  Hell, without even thinking about it!
bigimg:
  url: reduxjs-toolkit-immer-big.png
  prompt: "A dramatic split-screen: one side shows a chaotic red hot tangle of wires, which flows over to the other side which is a blue frozen cube"
  origin: Midjourney
img:
  url: reduxjs-toolkit-immer.png
  origin: Midjourney
  prompt: "A futuristic glass cube encapsulating dynamic swirling data streams, but the cube itself remains frozen in time. The background is a digital environment with blue and white neon elements, symbolizing immutable state updates in JavaScript"
categories: javascript
tags: [tutorial,react]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "The 'Mythical Zoos' example code and the talk pptx"
package-versions:
  - package: immer
    version: 9.0.21
toc:
  title: Immer
  icon: lock
socials:
  linkedin: ""
  facebook: ""
  twitter: ""
---

{% include github-stars.html url="immerjs/immer" desc="Create the next immutable state by mutating the current one" %}

Immer is the solution the ReduxJS/Toolkit has adopted to enforce pure reducers: it allows you to write reducers
while (almost) not having to think about immutability anymore!

Awards!? 🏆🥇
- React: [Breakthrough of the year 2019](https://osawards.com/react/2019)
- JavaScript: [Most impactful contribution 2019](https://osawards.com/javascript/2019)

<!--more-->

# Redux Bugs

Redux bugs are almost always cases where you didn't quite spread enough and/or did
an accidental mutation of the state.

This is exactly the problem immer addresses and does so in a way that you basically
don't know you're using immer until you inspect the type and see `WritableDraft<T>`.

# Example

## Without Immer

Do you really want to be doing this...

```js
function reducerSpreadHell(state, action) {
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue,
        },
      },
    },
  }
}
```

One could argue that you should be normalizing your store instead of
writing reducers like that, and you'd be right.

## With Immer

Still, with immer, all that becomes just this:

```js
state.first.second[action.someId].fourth = action.someValue;
```

# Immer Reducers

You'd write them as if you just didn't care about immutability!

```ts
import { current, PayloadAction } from "@reduxjs/toolkit";

reducers: {
  addTodo: (state: WritableDraft<Todo>[], action: PayloadAction<Todo>) => {
    // ReduxJS/Toolkit wraps the Todo[] state in a WritableDraft
    // "[].push()" does NOT mutate!
    // Same for pop, shift, splice etc...
    state.push(action.payload);
  },

  removeTodo: (state, action: PayloadAction<number>) => {
    // ATTN: state = newState does NOT work!
    // In such case, return the newState
    return state.filter(todo => todo.id !== action.payload);
  },

  // ATTN: Mutate or Return, but not both!
  altAddTodo: (state, action: PayloadAction<Todo>) => void state.push(action.payload),

  toggleTodo: (state, action: PayloadAction<number>) => {
    // If you want to look at the current state, you need to use current()
    // There is also original()
    console.log('current(state)', current(state));

    const todo = state.find(todo => todo.id === action.payload)!;
    todo.done = !todo.done;
  },

  updateTodo: (state, action: PayloadAction<Todo>) => {
    const index = state.findIndex(todo => todo.id === action.payload.id);
    if (index !== -1) {
      state[index] = action.payload;
    }
  },
},
```

## Caveats

There are some caveats like using `current()` when you want to look at the state.

### Replacing the entire state

As in the case of removeTodo, you cannot completely replace `state`.

```ts
// ⚠️⚠️ DOES NOT WORK ⚠️⚠️
removeTodo: (state, action: PayloadAction<number>) => {
  // This could only work if state would be "by ref"
  // which JavaScript doesn't have
  state = state.filter(todo => todo.id !== action.payload);
}

// ✔️✔️ DOES WORK ✔️✔️
removeTodo: (state, action: PayloadAction<number>) => {
  return state.filter(todo => todo.id !== action.payload);
}
```

### Reducers without curly braces

Use `void` for your one-liner reducers:

```ts
// ⚠️⚠️ TypeScript ERROR ⚠️⚠️
// [].push() returns a number which is not a valid new state
addTodo: (state, action: PayloadAction<Todo>) => state.push(action.payload)

// ✔️✔️ BOTH WORK ✔️✔️
addTodo: (state, action: PayloadAction<Todo>) => void state.push(action.payload)
addTodo: (state, action: PayloadAction<Todo>) => {
  state.push(action.payload);
}
```

### Primitives cannot be wrapped

If you extract a primitive value (number, bool, string) from the state
and then manipulate it, you're no longer working with a WritableDraft
and are just mutating the local variable.

```ts
addMessage: (state, action: PayloadAction<any>) => {
  // ⚠️⚠️ DOES NOT WORK ⚠️⚠️
  let { newMessagesCount } = state;
  newMessagesCount++;

  // ✔️✔️ DOES WORK ✔️✔️
  state.newMessagesCount++;
}
```


# Under the hood

Immer's bread and butter is the `produce(baseState, recipe)` function.
It takes your `baseState`, wraps it in a `WritableDraft` and
executes a `recipe` against it, which means calling your reducer
with the dispatched action. It then creates the new state
by re-applying the steps done in the recipe to the baseState,
but in a non-mutating way.

The ReduxJS/Toolkit reducers have already called `produce`,
so this has been abstracted away for us.

![The immer flow](/assets/blog-images/reduxjs-toolkit-immer-flow.png "The immer flow"){: .img-responsive}


## Produce Example

Small code example to illustrate how `produce` works.
In the accompanying git repository, the code is a bit more involved and includes tests
to prove that only the objects that changed actually have
different references!

```ts
import { produce } from "immer";

interface UpdatePizzaTopping {
  type: "🍄" | "🌶️" | "🥓";
  doubleIt: boolean;
}

const reducerTastyPizza = (baseState: PizzaState, action: UpdatePizzaTopping) => {
  const recipe = (draft: WritableDraft<PizzaState>) => {
    const topping = draft.toppings.find(topping => topping.type === action.type);
    if (!topping)
      return;

    topping.doubleIt = action.doubleIt;
  }

  const newState: PizzaState = produce(baseState, recipe);
  return newState;
}
```

Which you would typically just write like so:

```ts
const reducerTastyPizza = (state: PizzaState, action: UpdatePizzaTopping) => {
  return produce(state, draft => { /* do stuff here! */ });
}
```

# Outside of ReduxJS/Toolkit

Immer can be useful even if you are not using ReduxJS/Toolkit. Or
just outside your reducers -- you already have the dependency anyway!

In a form Component, you might have something like

```ts
const [title, setTitle] = useState("");
const [text, setText] = useState("");
// If you've got 10 properties, then 10x useState???
```

## Nimmer!

```ts
import { produce } from "immer";

const TodoAdd = () => {
  const [todo, setTodo] = useState<Todo>(produce(() => ({
    title: '',
    text: '',
  })));

  return (
    <>
      <input
        value={todo.title}
        onChange={e => setTodo(produce(todo, draft => {draft.title = e.target.value}))}
      />
      <textarea
        value={todo.text}
        onChange={e => setTodo(produce(todo, draft => {draft.text = e.target.value}))}
      />
    </>
  );
}
```

Uhoh, `setTodo(produce(todo, draft...))`, might as well stick with my 10x useState!

You can make this a lot nicer with a helper function, while still keeping
everything type-safe:

```ts
const updateTodo = <K extends keyof Todo>(key: K, value: Todo[K]) => {
  setTodo(produce(todo, draft => {
    draft[key] = value;
  }));
}

return (
  <input value={todo.title} onChange={e => updateTodo('title', e.target.value)} />
)
```

## useImmer

But of course there is a package for that!

{% include github-stars.html url="immerjs/use-immer" desc="Use immer to drive state with a React hooks" %}

```ts
import { useImmer } from "use-immer";

const TodoAdd = () => {
  const [todo, setTodo] = useImmer<Todo>({
    title: '',
    text: '',
  });

  return (
    <>
      <input
        value={todo.title}
        onChange={e => setTodo(draft => draft.title = e.target.value)}
      />
      <textarea
        value={todo.text}
        onChange={e => setTodo(draft => draft.text = e.target.value)}
      />
    </>
  );
}
```

`use-immer` also exposes `useImmerReducer` to replace your `useReducer`s.
