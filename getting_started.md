---
title: Getting Started with Zustand
description: Learn how to install and use Zustand for state management in React applications
nav: 1
---

# Getting Started with Zustand

Zustand is a small, fast, and scalable state management solution for React applications. It provides a simple and intuitive API based on hooks, making it easy to integrate into your projects without excessive boilerplate.

## Installation

To start using Zustand in your project, install it using your preferred package manager:

```bash
# Using npm
npm install zustand

# Using yarn
yarn add zustand

# Using pnpm
pnpm add zustand
```

## Creating a Store

With Zustand, you create a store using the `create` function. Your store is essentially a hook that can contain any type of state: primitives, objects, or functions.

Here's a basic example of creating a store:

```javascript
import { create } from 'zustand'

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
```

In this example:
- We define a store with a `bears` state.
- `increasePopulation` is an action that increases the bear count.
- `removeAllBears` is an action that resets the bear count to zero.

The `set` function is used to update the state. It merges the new state with the existing state.

## Using the Store in React Components

Once you've created a store, you can use it in any React component without needing to wrap your app in providers. Here's how you can consume the store:

```jsx
import React from 'react'
import { useStore } from './store' // import your store

function BearCounter() {
  const bears = useStore((state) => state.bears)
  return <h1>{bears} bears around here...</h1>
}

function Controls() {
  const increasePopulation = useStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>One up</button>
}
```

In these components:
- `BearCounter` selects and displays the `bears` state from the store.
- `Controls` uses the `increasePopulation` action from the store.

The components will automatically re-render when the selected state changes.

## Selecting Multiple State Slices

You can select multiple state slices in a single component:

```jsx
function BearInfo() {
  const { bears, increasePopulation, removeAllBears } = useStore(state => ({
    bears: state.bears,
    increasePopulation: state.increasePopulation,
    removeAllBears: state.removeAllBears
  }))

  return (
    <div>
      <h1>{bears} bears around here...</h1>
      <button onClick={increasePopulation}>One up</button>
      <button onClick={removeAllBears}>Remove all</button>
    </div>
  )
}
```

This approach allows you to access multiple state properties and actions in a single component.

## Next Steps

Now that you've got the basics down, you can explore more advanced features of Zustand:

- Middleware for added functionality (e.g., persist, devtools)
- Async actions
- TypeScript usage
- Optimizing re-renders

Check out the other sections of the documentation to dive deeper into these topics and more!