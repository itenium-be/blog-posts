---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit: createApi"
subTitle: "RTK Query"
date: 2025-03-09
desc: >
  The highest level of abstraction and the recommended way
  to use the ReduxJS/Toolkit for new applications.
bigimg:
  url: reduxjs-toolkit-createApi-big.png
  prompt: "A fun, cartoon-style warehouse with conveyor belts moving packages to the right destinations. Workers scan each package before it gets sent out, ensuring everything is stored and delivered correctly. The scene is lively and full of action, with an organized but playful feel."
  origin: Midjourney
img:
  url: reduxjs-toolkit-createApi.png
  origin: ChatGPT
  prompt: "A whimsical restaurant kitchen where a chef takes an order from a floating menu and magically assembles the perfect dish. The ingredients appear instantly from thin air, representing data fetched on demand. The scene is fun, dynamic, and full of vibrant colors."
categories: javascript
tags: [tutorial,react]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "The 'Mythical Zoos' example code and the talk pptx"
toc:
  title: createApi
  icon: icon-reactjs
socials:
  linkedin: ""
  facebook: ""
  twitter: ""
---

So far, what we've done is a pretty much a 1 to 1 conversion of legacy Redux
to the more modern toolkit, albeit with highly reduced boilerplate. We can
however, take it a step further still with **RTK Query**.

When you call `createAsyncThunk` directly, there is still some ceremony
you have to take care off. Aside from handling pending/fulfilled extraReducers
you also need to think about "stale data": When do I refetch? How do
I avoid requesting the same data multiple times when rendering a page?

<!--more-->

# Example

```ts
// The import from "query/react" generates React hooks!
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

// Typically called once per app with one API slice per base url
export const zooApi = createApi({
  reducerPath: 'zoo',

  // fetchBaseQuery is a small wrapper around fetch
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  endpoints: build => ({
    getZoos: build.query<DbZoo[], void>({
      query: () => '/zoos',
      //providesTags: ['zoos'],
    }),
    // getZoo: build.query<MythicalZoo, number>({
    //   query: id => `/zoos/${id}`,
    //   providesTags: (result, error, id) => [{ type: 'zooDetails', id }],
    //   keepUnusedDataFor: 30, // in seconds

    //   // Transform the response in case the data is not how you'd like it to be
    //   transformResponse: (response: ApiResponse<MythicalZoo>) => response.data,
    //   // Also: transformErrorResponse
    // }),

    // addVisitor: build.mutation<MythicalZoo, Partial<Visitor> & {zooId: number}>({
    //   query: ({ zooId, ...visitor }) => ({
    //     url: `/zoos/${zooId}`,
    //     method: 'POST',
    //     body: visitor,
    //   }),
    //   invalidatesTags: (result, error, { zooId }) => {
    //     // Invalidate only the cache for the current zoo details
    //     return [{type: 'zooDetails', id: zooId }];
    //   },
    //   async onQueryStarted({ zooId, ...visitor }, { dispatch, getState, queryFulfilled, requestId, extra, getCacheEntry }) {
    //     // Already show the new Visitor in the UI as if the request would succeed:
    //     const patchResult = dispatch(
    //       zooApi.util.updateQueryData("getZoo", zooId, draft => {
    //         const tempVisitor: Visitor = {
    //           id: 99,
    //           name: visitor.name ?? "",
    //           type: visitor.type ?? "🧙",
    //           ticketType: visitor.ticketType ?? "🎫 Standard",
    //           favoriteCreatures: [],
    //         }
    //         draft.visitors.push(tempVisitor);
    //       })
    //     );

    //     try {
    //       await queryFulfilled;
    //     } catch {
    //       patchResult.undo();
    //     }

    //     // NEXT: Migrating to RTK Query might be quite hard, maybe you want to take a step back
    //     // NEXT: and stick to a more low level API -- let's look at createAsyncThunk in wilds/wildsSlice.ts
    //   },
    //   async onCacheEntryAdded(arg, { dispatch, getState, extra, requestId, cacheEntryRemoved, cacheDataLoaded, getCacheEntry, }) {
    //   },
    // }),
  })
});

export const { useGetZoosQuery, useGetZooQuery, useAddVisitorMutation } = zooApi;
```

## Store Configuration

```ts
export const store = configureStore({
  reducer: {
    // Add the generated reducer as a specific top-level slice
    [zooApi.reducerPath]: zooApi.reducer,
  },
  // Adding the api middleware enables caching, invalidation, polling,
  // and other useful features of `rtk-query`.
  middleware: getDefaultMiddleware => getDefaultMiddleware()
    .concat(zooApi.middleware),
});
```


## fetchBaseQuery

https://redux-toolkit.js.org/rtk-query/api/fetchBaseQuery

```ts
const baseQuery = fetchBaseQuery({
  baseUrl: '/',
  prepareHeaders: (headers, { getState }) => {
    const token = (getState() as RootState).auth.token;
    if (token) {
      headers.set('authorization', `Bearer ${token}`)
    }
    return headers;
  },
})
```


# Provider vs ApiProvider

https://redux-toolkit.js.org/rtk-query/api/ApiProvider

<ApiProvider />: Can be used as a Provider if you do not already have a Redux store.




https://redux-toolkit.js.org/api/combineSlices
--> needs the createSlice({reducerPath?: string,}) ?

// reducerPath:
// A preference for the slice reducer's location, used by `combineSlices` and `slice.selectors`. Defaults to `name`.


name: 'todoActionPrefix',
// The slice reducer's location, used by 'slice.selectors'. Defaults to `name`.
reducerPath: 'todos',

# Listeners

https://redux-toolkit.js.org/rtk-query/api/setupListeners


```ts
import { setupListeners } from "@reduxjs/toolkit/query";

const store = configureStore({});

// To enable refetchOnMount and refetchOnReconnect:
setupListeners(store.dispatch);
```



# Alternatives

RTK Query was not the first library to handle these concerns:

{% include github-stars.html url="TanStack/query" desc="🤖 Powerful asynchronous state management, server-state utilities and data fetching" %}
{% include github-stars.html url="apollographql/apollo-client" desc="🚀 A fully-featured, production ready caching GraphQL client" %}
{% include github-stars.html url="vercel/swr" desc="React Hooks for Data Fetching" %}
{% include github-stars.html url="remix-run/react-router" desc="Declarative routing for React" %}

Check this [TanStack/query sheet](https://tanstack.com/query/latest/docs/framework/react/comparison)
for an in-depth comparison.
