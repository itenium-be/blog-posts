---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit: createAsyncThunk"
subTitle: "Let's do some API calls shall we"
date: 2025-02-13
desc: >
  Need that Rest API to fill the store.
  ReduxJS/Toolkit comes with redux-thunk and the
  createAsyncThunk convenience function.
bigimg:
  url: reduxjs-toolkit-createAsyncThunk-big.png
  prompt: "A futuristic power core generating structured energy waves, symbolizing controlled asynchronous state management. Three main glowing rings labeled 'Pending', 'Fulfilled', and 'Rejected' orbit the core, shifting dynamically based on data flow. The environment is a sleek cybernetic world with deep blue, purple, and orange hues, evoking an advanced state-processing system."
  origin: Midjourney
img:
  url: reduxjs-toolkit-createAsyncThunk.png
  origin: ChatGPT
  prompt: "A high-tech command center where a digital AI assistant dispatches glowing data packets through multiple interconnected pathways labeled 'Pending', 'Fulfilled', and 'Rejected'. The room is filled with floating holographic screens displaying API call results and Redux actions in real time. The colors are vibrant, dominated by neon blues and purples, with a sleek, futuristic UI aesthetic."
categories: javascript
tags: [tutorial,react]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "The 'Mythical Zoos' example code and the talk pptx"
package-versions:
  - package: "redux-thunk"
    version: 3.1.0
toc:
  title: createAsyncThunk
  icon: icon-reactjs
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_itenium-keeponlearning-reduxtoolkit-activity-7323647627009429504-q0pP"
  facebook: "https://www.facebook.com/share/p/1LfAF12rs5/"
  twitter: "https://x.com/itenium_be/status/1917881844881657955"
  instagram: "https://www.instagram.com/p/DJG1fzMColG/"
---

`createAsyncThunk` generates promise lifecycle action types, which you
handle in the `extraReducers` of your slice.

```ts
import { createAsyncThunk } from "@reduxjs/toolkit";

export const fetchStuff = createAsyncThunk(
  'resource/action',
  async (params, thunkApi) => {
    try {
      const response = await fetch(`/api/resource/${params.id}`);
      return await response.json();
    } catch {
      // Also possible to return a rejected Promise
      return thunkApi.rejectWithValue("Well, that failed");
    }
  }
);
```

This dispatches the following actions (from the first "Type" argument):

- `resource/action/pending`: display a spinner or something?
- `resource/action/rejected`: display an error message?
- `resource/action/fulfilled`: while the previous two could be considered optional, you definitely want to handle this one!

<!--more-->

Check [Promise Lifecycle Actions](https://redux-toolkit.js.org/api/createAsyncThunk#promise-lifecycle-actions)
for the exact signatures of the pending/rejected/fulfilled actions.

# Reduce It

```ts
const mySlice = createSlice({
  extraReducers: builder => {
    builder.addCase(fetchStuff.fulfilled, (state, action) => {
      // action.payload is what fetchStuff returned
    });

    builder.addCase(fetchStuff.pending, (state, action) => {});
    builder.addCase(fetchStuff.rejected, (state, action) => {
      // action.payload === 'Well, that failed'
      // action.error.message === 'Rejected'
    });

    builder.addMatcher(fetchStuff.settled, (state, action) => {
      // Called for both fulfilled and rejected
    });
  }
});
```

# Parameters

```ts
createAsyncThunk(Type, PayloadCreator, [Options]);
```

## 1. Type

A string that will be prepended with the three states and dispatched as they happen.

## 2. PayloadCreator

```ts
async (params, thunkApi) => {}
```

- `params`: What you'll call the thunk with. This could be the request body, an id, queryString variables etc
- `thunkApi`: An object containing
  - `dispatch`: To dispatch actions other than the three that are generated
  - `getState`: Gets the current root store state
  - `extra`: The argument passed to `configureStore({middleware: getDefaultMiddleware => getDefaultMiddleware({thunk: {extraArgument: "**this**"}})})`
  - `requestId`: unique generated ID
  - `signal`: [AbortController.signal](https://developer.mozilla.org/en-US/docs/Web/API/AbortController/signal) when you want to implement cancellation (see [Cancellation](https://redux-toolkit.js.org/api/createAsyncThunk#cancellation))
  - `rejectWithValue(value; [meta])` and `fulfillWithValue(value, [meta])`: or just throw/return!

## 3. Options

An option object that can contain:

- `condition`: if this function returns false, no actions will be dispatched, see [Canceling before execution](https://redux-toolkit.js.org/api/createAsyncThunk#canceling-before-execution)
  - `dispatchConditionRejection`: if `condition` returned false and this field is true, the rejected action will still be dispatched
- `idGenerator`: Uses [nanoid](https://redux-toolkit.js.org/api/other-exports#nanoid) by default
- `serializeError`: Serialize errors in a different way than [sindresorhus/serialize-error](https://github.com/sindresorhus/serialize-error)
- `getPendingMeta`: merge extra data into `pendingAction.meta`


# Unwrapping

`createAsyncThunk` always returns a resolved promise, even if it was rejected.
To get the actual contents, `.unwrap()` it!

```ts
dispatch(fetchStuff({id: 42}))
  .unwrap()
  .then(success => {})
  .catch(error => {})
```

# Matchers

We already saw the `fetchStuff.settled` (for handling fulfilled or rejected) matcher above but there
are [other ways](https://redux-toolkit.js.org/api/matching-utilities#createasyncthunk-specific-matchers)
to handle multiple actions with a single matcher.

```ts
import { isPending } from "@reduxjs/toolkit";

type GenericAsyncThunk = AsyncThunk<unknown, unknown, any>
type PendingAction = ReturnType<GenericAsyncThunk['pending']>
type RejectedAction = ReturnType<GenericAsyncThunk['rejected']>
type FulfilledAction = ReturnType<GenericAsyncThunk['fulfilled']>

const mySlice = createSlice({
  extraReducers: builder => {
    builder.addMatcher<RejectedAction>(
      action => action.type.endsWith('/rejected'),
      (state, action) => {
        state.status = 'failed';
      }
    );

    builder.addMatcher(isPending, (state, action) => {
      // Or by using the existing isPending matcher utility
      // Other utilities: isAllOf, isAnyOf
      // isAsyncThunkAction, isFulfilled, isRejected(WithValue)
    });

    builder.addDefaultCase((state, action) => {
      // Handle everything that was not previously handled
      // Contrast to addMatcher which may handle an action
      // that was already handled by other case / matchers
    });
  }
});
```


# Reducer Creator

This alternative `reducers` creation allows you to add thunks
without having to revert to `extraReducers`.

```ts
import { buildCreateSlice, asyncThunkCreator } from "@reduxjs/toolkit";

// Extra setup required for adding thunks to reducers
const createAppSlice = buildCreateSlice({
  creators: { asyncThunk: asyncThunkCreator },
});

const creatorSlice = createAppSlice({
  initialState: [],
  reducers: create => ({
    deleteEntity: create.reducer<{id: number}>((state, action) => {
      return state.filter(x => x.id !== action.payload);
    }),

    addPartial: create.preparedReducer(
      (todo: Partial<Todo>) => {
        return {
          payload: {
            text: 'Default Text',
            ...todo,
          }
        }
      },
      (state, action) => {
        state.push(action.payload);
      }
    ),

    // See above, requires buildCreateSlice()
    getCreatures: create.asyncThunk(
      async (params, thunkApi): Promise<MyEntity> => {
        const res = await fetch(`/api/resource/${params.id}`);
        return res.json();
      }, {
        pending: state => {
          console.log('Getting entity');
        },
        rejected: (state, action) => {
          console.log('Failed getting entity');
        },
        fulfilled: (state, action) => {
          state.push(action.payload);
        }
      }
    ),
  }),
});
```
