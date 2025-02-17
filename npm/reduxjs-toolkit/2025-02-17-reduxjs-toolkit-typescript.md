---
layout: post
author: Wouter Van Schandevijl
title: "ReduxJS/Toolkit: TypeScript"
subTitle: "How to keep all this type-safe"
date: 2025-02-17
desc: >
  Avoiding getState() as RootState, like a champ!
bigimg:
  url: reduxjs-toolkit-typescript-big.png
  prompt: "A powerful cybernetic guardian stands at the gateway of a vast digital network, ensuring only structured, validated data can pass through. Glowing energy pipelines stretch into the distance, representing seamless data flow. The guardian emanates a futuristic glow, embodying security, precision, and advanced system integrity."
  origin: Midjourney
img:
  url: reduxjs-toolkit-typescript.png
  origin: ChatGPT
  prompt: "A futuristic AI engineer meticulously assembling complex, interconnected digital components inside a glowing cybernetic workspace. Holographic interfaces float around, displaying intricate patterns of structured data and system logic. The environment is sleek and futuristic, radiating an aura of precision and technological mastery."
categories: javascript
tags: [tutorial,react]
series: reduxjs-toolkit
extras:
  - githubproject: https://github.com/itenium-be/reduxjs-toolkit
    githubtext: "The 'Mythical Zoos' example code and the talk pptx"
toc:
  title: TypeScript
  icon: icon-reactjs
socials:
  linkedin: ""
  facebook: ""
  twitter: ""
---

You don't want to be using `useDispatch`, `useSelector` etc directly
in your code. Instead they are wrapped so that you can enjoy complete
type safety!

<!--more-->

# TypeScript Hooks

```ts
import { configureStore } from "@reduxjs/toolkit";
import { useDispatch, useSelector } from "react-redux";

const store = configureStore({});
export type AppDispatch = typeof store.dispatch;
export const useAppDispatch = useDispatch.withTypes<AppDispatch>();

export type RootState = ReturnType<typeof store.getState>;
export const useAppSelector = useSelector.withTypes<RootState>();
```

# createAsyncThunk

[More info](https://redux-toolkit.js.org/usage/usage-with-typescript#defining-a-pre-typed-createasyncthunk)

```ts
import { Action, createAsyncThunk, ThunkAction } from "@reduxjs/toolkit";
export type AppThunk<ThunkReturnType = void> = ThunkAction<ThunkReturnType, RootState, unknown, Action>;
export const createAppAsyncThunk = createAsyncThunk.withTypes<{state: RootState, dispatch: AppDispatch}>();

// Complete example:
const createAppAsyncThunk = createAsyncThunk.withTypes<{
  state: RootState
  dispatch: AppDispatch
  rejectValue: string
  extra: { whatever: number }
}>()
```

# createSelector

```ts
// Wrapping reselect createSelector
import { createSelector } from "@reduxjs/toolkit";
const createAppSelector = createSelector.withTypes<RootState>();
```
