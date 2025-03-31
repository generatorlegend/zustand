---
title: Core Concepts
description: Understanding the fundamental concepts of Zustand
nav: 1
---

# Core Concepts

Zustand is a lightweight state management library for React applications. It provides a simple and intuitive API for managing global state without the complexity of some other state management solutions. This guide will explain the core concepts of Zustand, including store creation, state updates, and subscribing to state changes.

## Store Creation

In Zustand, a store is created using the `create` function. This function takes a callback that defines the initial state and actions to update the state.

```javascript
import { create } from 'zustand'

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
```

The `create` function returns a hook that can be used in your React components to access the store's state and actions.

## State Updates

Zustand provides a `set` function to update the state. This function can be used in two ways:

1. Passing an object to merge with the current state:

```javascript
set({ bears: 5 })
```

2. Passing a function that receives the current state and returns an object to merge:

```javascript
set((state) => ({ bears: state.bears + 1 }))
```

The `set` function automatically merges the new state with the existing state, allowing for partial updates.

## Subscribing to State Changes

Zustand makes it easy to subscribe to state changes in your React components. You can use the store hook directly in your components and select the specific state you need:

```jsx
function BearCounter() {
  const bears = useStore((state) => state.bears)
  return <h1>{bears} bears around here...</h1>
}

function Controls() {
  const increasePopulation = useStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>Add a bear</button>
}
```

Zustand will automatically re-render your components when the selected state changes, ensuring optimal performance.

## Comparison with Other State Management Solutions

Zustand offers several advantages over other state management libraries:

1. **Simplicity**: Zustand has a minimal API and requires less boilerplate code compared to libraries like Redux.

2. **No Provider required**: Unlike Context API or Redux, Zustand doesn't need a provider component wrapping your app.

3. **TypeScript support**: Zustand is written in TypeScript and provides excellent type inference out of the box.

4. **Performance**: Zustand uses React's `useSyncExternalStore` hook internally, ensuring optimal performance and compatibility with React's concurrent features.

5. **Flexible middleware system**: Zustand allows you to extend its functionality through middleware, similar to Redux but with a simpler API.

## Conclusion

Zustand's core concepts revolve around creating stores, updating state, and subscribing to state changes in a simple and efficient manner. By leveraging these concepts, you can build scalable and performant React applications with ease.

For more advanced usage and API details, refer to the other sections of the documentation.