---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit: createSlice and configureStore"
subTitle: "The very minimum you need to know"
date: 2025-02-08
desc: >
  If you just want to jump into the Toolkit,
  this is what you need to know.
bigimg:
  url: reduxjs-toolkit-createSlice-big.png
  prompt: "slices"
  origin: Midjourney
img:
  url: "reduxjs-toolkit-createStore.png"
  origin: ChatGPT
  prompt: "A detailed, blueprint-style illustration of a 1950s 'redux store' under construction. he image has a sepia-toned nostalgic feel, with sketched lines and a vintage Americana aesthetic."
categories: javascript
tags: [tutorial,react]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "The 'Mythical Zoos' example code and the talk pptx"
package-versions:
  - package: "@reduxjs/toolkit"
    version: 2.5.0
toc:
  title: ReduxJS/Toolkit
  icon: icon-reactjs
socials:
  linkedin: ""
  facebook: ""
  twitter: ""
---

The legacy Redux `createStore` could make a grown man cry, and probably has.
`createSlice` and `configureStore` GREATLY simplifies this process while at the same
time also significantly reducing the boilerplate -- you can basically delete
all your actions as they are now defined by the reducers!

- `createSlice`: each top level object of your state is typically a slice
- `configureStore`: abstracts away the horror that was `createStore`

<!--more-->

# createSlice

```ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

export const todoSlice = createSlice({
  // Action prefix: todos/addTodo, todos/removeTodo etc
  name: "todos",
  initialState: [] as Todo[],
  reducers: {
    // A default reducer:
    // The actions are exported from the reducers (below)
    removeTodo: (state, action: PayloadAction<number>) => {
      return state.filter(todo => todo.id !== action.payload);
    },

    // A prepared reducer
    // When you need to modify the payload before handling
    addTodo: {
      reducer: (state, action: PayloadAction<Todo>) => {
        state.push(action.payload);
      },
      prepare: (todo: Partial<Todo>) => {
        // The addTodo action takes a Partial<Todo>
        // But the reducer handles a complete Todo.
        const payload = {
          text: 'Default Text',
          ...todo,
        };
        return { payload };
      }
    },
  },


  // We'll get into more detail in Part 3: createAsyncThunk
  extraReducers: builder => {
    // This is where we would handle the pending, fulfilled and rejected actions
    // from a createAsyncThunk

    // Handling an action from another slice is also a use case for extraReducers
    builder.addCase(addCustomer, (state, action: PayloadAction<{name: string}>) => {
      state.push({text: `Onboard new customer ${action.payload.name}`});
    });
  },


  // We'll get into more detail in Part 5: createSelector
  selectors: {
    selectTodo: (state, id: number): Todo | undefined => {
      return state.find(x => x.id === id);
    },
  }
});

export const { addTodo, removeTodo } = todoSlice.actions;
export const { selectTodo } = todoSlice.selectors;
```

## Dispatching an action

See Part 4: TypeScript for how to create the `useAppDispatch` hook.

```ts
import { addTodo } from "./todoSlice";

const TodoAdd = () => {
  const dispatch = useAppDispatch();
  const [todo, setTodo] = useState<Todo>({});
  return <button onClick={() => dispatch(addTodo(todo))}>Add</button>
}
```


# configureStore

Once you've defined some slices, time to combine them into your store.

```ts
// Create the store
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: {
    todos: todoSlice.reducer,
    user: userSlice.reducer,
  },

  // DevTools extension is added by default
  // Here we disable it for production:
  devTools: process.env.NODE_ENV !== 'production',

  // Optionally hydrate the store from somewhere:
  preloadedState: localStorage.getItem('redux'),
});


// Bind it to our main <App /> component
import { createRoot } from "react-dom/client";
import { Provider } from "react-redux";

const root = createRoot(
  document.getElementById('root')
);
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

This will create a store with "RootState":

```ts
type RootState = {
  todos: ReturnType<typeof todoSlice.getInitialState>;
  user: ReturnType<typeof userSlice.getInitialState>;
}
```

It really doesn't get any easier than that. Without overriding
anything, this will also setup the must have 
[Redux DevTools Extension](https://github.com/reduxjs/redux-devtools) (⭐ 13k).

This can be overriden/configured by passing `devTools: false | DevToolsOptions` to `configureStore`.


## Middleware

The [redux-thunk](https://github.com/reduxjs/redux-thunk) middleware is added by default,
since you are going to be doing some http calls 😊

During development builds, the following middleware
is also [automatically added](https://redux-toolkit.js.org/api/getDefaultMiddleware)

- [ImmutableStateInvariantMiddleware](https://redux-toolkit.js.org/api/immutabilityMiddleware): detects any mutations to state
- [SerializableStateInvariantMiddleware](https://redux-toolkit.js.org/api/serializabilityMiddleware): detects any non-serializable values in state or actions
- [ActionCreatorInvariantMiddleware](https://redux-toolkit.js.org/api/actionCreatorMiddleware): detects if an action creator has been dispatched (an action creator should've been called before being dispatched)


```ts
// Extend the default middleware
configureStore({
  middleware: getDefaultMiddleware => getDefaultMiddleware()
    .concat(loggingMiddleware, persistMiddleware)
    .prepend(listenerMiddleware.middleware)
});

// Explicitly configure your own middleware
configureStore({
  middleware: () => new Tuple(myMiddleware, logger),
})

// Enable/Disable or configure the default middleware
configureStore({
  middleware: getDefaultMiddleware => getDefaultMiddleware({
    // Always added (unless explicitly passing false!)
    thunk: boolean | {
      extraArgument: any
    },

    // Only added during development:
    immutableCheck: boolean | {
      isImmutable: not object, null or undefined,
      ignoredPaths: undefined,
      warnAfter: 32ms,
    },
    serializableCheck: boolean | {
      isSerializable: (value: any) => boolean,
      ignoredActions: string[],
      ignoreState: boolean,
      ignoreActions: boolean,
      ...
    },
    actionCreatorCheck: boolean | {
      isActionCreator: (action: unknown) => action is Function & { type?: unknown }
    }
  })
});
```

### Custom Middleware

Some ready made redux middlewares:

{% include github-stars.html url="reduxjs/redux-thunk" desc="Thunk middleware for Redux (included)" %}
{% include github-stars.html url="rt2zz/redux-persist" desc="Persist and rehydrate a redux store" %}
{% include github-stars.html url="redux-observable/redux-observable" desc="RxJS middleware for action side effects using 'Epics'" %}
{% include github-stars.html url="LogRocket/redux-logger" desc="Logger for Redux" %}
{% include github-stars.html url="omnidan/redux-undo" desc="♻️ higher order reducer to add undo/redo" %}
{% include github-stars.html url="redux-utilities/redux-promise" desc="Dispatch promises" %}
{% include github-stars.html url="agraboso/redux-api-middleware" desc="Redux middleware for calling an API." %}

Or roll your own:

```ts
export const loggingMiddleware = ({dispatch, getState}) => next => action => {
  console.log('Dispatching', action);
  const result = next(action);
  console.log('State after dispatch', getState());
  return result;
}

// redux-promise is basically a simplified version of redux-thunk
export const resolvePromiseMiddleware = ({dispatch, getState}) => next => action => {
  if (action.payload instanceof Promise) {
    return action.payload.then(result => dispatch({...action, payload: result}));
  }
  return next(action);
}
```

When writing middleware that is only going to do something for specific actions,
check out the [matching utilities](https://redux-toolkit.js.org/api/matching-utilities) provided by the toolkit.

If you need to modify middleware after store creation, you can do so with [createDynamicMiddleware](https://redux-toolkit.js.org/api/createDynamicMiddleware).

[Listener middleware](https://redux-toolkit.js.org/api/createListenerMiddleware) is what ReduxJS/Toolkit
currently recommends using instead of using something like [`redux-saga`](https://github.com/redux-saga/redux-saga) or `redux-observable`.


## Enhancers

Middleware is added to the store by the [`applyMiddleware`](https://redux.js.org/api/applymiddleware)
enhancer. Enhancers are the most powerful extension method available in Redux and it's unlikely
you're going to be writing any yourself.

```ts
const store = configureStore({
  enhancers: getDefaultEnhancers => getDefaultEnhancers().concat(...),
})
```

The by default added enhancers are DevTools
and [autoBatchEnhancer](https://redux-toolkit.js.org/api/autoBatchEnhancer).


### Middleware vs Enhancers

| Feature                      | Middleware   | Store Enhancer |
|------------------------------|--------------|----------------|
| **Intercepts actions?**      | ✅ Yes       | ❌ No         |
| **Modifies store behavior?** | ❌ No        | ✅ Yes        |
| **Used for side effects?**   | ✅ Yes       | ❌ No         |
| **Applied via?**             | `middleware` | `enhancers`    |
| **Example**                  | Action logger| DevTools       |
{: .table-code}
