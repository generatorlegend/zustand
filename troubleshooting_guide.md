---
title: Troubleshooting Guide
description: Common issues and solutions when using Zustand
nav: 8
---

# Zustand Troubleshooting Guide

This guide covers common issues you might encounter when using Zustand and provides solutions and workarounds for each problem.

## Table of Contents

1. [State Updates Not Reflecting](#state-updates-not-reflecting)
2. [React Integration Issues](#react-integration-issues)
3. [TypeScript Errors](#typescript-errors)
4. [Middleware Usage Problems](#middleware-usage-problems)

## State Updates Not Reflecting

### Issue: Component not re-rendering on state change

**Problem**: Your component doesn't update when you change the state in your Zustand store.

**Solution**: 

1. Make sure you're using the `useStore` hook correctly:

```jsx
const bears = useStore((state) => state.bears)
```

2. Ensure you're not accidentally creating a new reference in your selector:

```jsx
// Incorrect
const { bears } = useStore((state) => ({ bears: state.bears }))

// Correct
const bears = useStore((state) => state.bears)
```

3. If you need to select multiple values, consider using a memoized selector:

```jsx
import { shallow } from 'zustand/shallow'

const { bears, incrementBears } = useStore(
  (state) => ({ bears: state.bears, incrementBears: state.incrementBears }),
  shallow
)
```

### Issue: State updates not persisting

**Problem**: Your state updates seem to disappear after component re-renders.

**Solution**: 

Ensure you're updating the state immutably:

```jsx
// Incorrect
set((state) => {
  state.bears += 1
  return state
})

// Correct
set((state) => ({ bears: state.bears + 1 }))
```

## React Integration Issues

### Issue: Zustand store not working with React concurrent mode

**Problem**: Your Zustand store behaves unexpectedly when using React concurrent mode.

**Solution**:

Zustand is designed to work with React concurrent mode out of the box. If you're experiencing issues:

1. Ensure you're using the latest version of Zustand.
2. Avoid using `useLayoutEffect` with Zustand stores, as it can cause issues in concurrent mode.
3. If problems persist, consider wrapping your store creation with `unstable_batchedUpdates`:

```jsx
import { unstable_batchedUpdates } from 'react-dom'
import { create } from 'zustand'

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => 
    unstable_batchedUpdates(() => {
      set((state) => ({ bears: state.bears + 1 }))
    }),
}))
```

## TypeScript Errors

### Issue: TypeScript errors when creating a store

**Problem**: You encounter TypeScript errors when trying to create a Zustand store.

**Solution**:

1. Make sure you're using the correct syntax for TypeScript:

```typescript
import { create } from 'zustand'

interface BearState {
  bears: number
  increase: (by: number) => void
}

const useBearStore = create<BearState>()((set) => ({
  bears: 0,
  increase: (by) => set((state) => ({ bears: state.bears + by })),
}))
```

2. If you're using middlewares, ensure you're providing the correct type annotations:

```typescript
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

const useBearStore = create<BearState>()(
  devtools(
    persist(
      (set) => ({
        bears: 0,
        increase: (by) => set((state) => ({ bears: state.bears + by })),
      }),
      { name: 'bear-storage' }
    )
  )
)
```

### Issue: TypeScript errors with selectors

**Problem**: You're getting TypeScript errors when using selectors with `useStore`.

**Solution**:

Ensure you're providing the correct type for the selector function:

```typescript
const bears = useStore((state: BearState) => state.bears)
```

## Middleware Usage Problems

### Issue: Persist middleware not saving state

**Problem**: The persist middleware is not saving your state to localStorage.

**Solution**:

1. Check if you've configured the persist middleware correctly:

```javascript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

const useBearStore = create(
  persist(
    (set) => ({
      bears: 0,
      addBear: () => set((state) => ({ bears: state.bears + 1 })),
    }),
    {
      name: 'bear-storage', // unique name
      getStorage: () => localStorage, // (optional) by default, 'localStorage' is used
    }
  )
)
```

2. Ensure that localStorage is available in your environment.

3. If you're using a custom storage engine, make sure it implements the correct interface.

### Issue: Devtools middleware not showing updates

**Problem**: The Redux DevTools extension is not showing updates from your Zustand store.

**Solution**:

1. Make sure you've installed the Redux DevTools extension in your browser.

2. Ensure you've set up the devtools middleware correctly:

```javascript
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useBearStore = create(
  devtools(
    (set) => ({
      bears: 0,
      addBear: () => set((state) => ({ bears: state.bears + 1 })),
    })
  )
)
```

3. If you're using multiple middlewares, ensure devtools is the outermost middleware:

```javascript
const useBearStore = create(
  devtools(
    persist(
      (set) => ({
        bears: 0,
        addBear: () => set((state) => ({ bears: state.bears + 1 })),
      }),
      { name: 'bear-storage' }
    )
  )
)
```

By addressing these common issues, you should be able to resolve most problems encountered when using Zustand. If you're still facing difficulties, consider checking the [Zustand GitHub issues](https://github.com/pmndrs/zustand/issues) for more specific problems and solutions.