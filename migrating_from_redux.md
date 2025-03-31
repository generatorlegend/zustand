---
title: Migrating from Redux to Zustand
description: A guide to help developers transition from Redux to Zustand for state management
nav: 5
---

# Migrating from Redux to Zustand

If you're considering migrating your React application's state management from Redux to Zustand, this guide will help you understand the key differences and how to refactor your code. Zustand offers a simpler and more lightweight approach to state management, which can lead to improved performance and developer experience.

## Key Differences

1. **Setup**: Zustand doesn't require a provider, making it easier to set up and use throughout your application.
2. **Store Creation**: In Zustand, stores are created using hooks, whereas Redux uses a more complex setup with reducers and actions.
3. **Updating State**: Zustand allows for direct state updates, while Redux requires actions and reducers.
4. **Middleware**: Zustand has built-in middleware support, including a Redux-like middleware for easier migration.

## Creating a Store

### Redux
```javascript
// Redux
import { createStore } from 'redux';

const initialState = { bears: 0 };

function reducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, bears: state.bears + 1 };
    case 'RESET':
      return { ...state, bears: 0 };
    default:
      return state;
  }
}

const store = createStore(reducer);
```

### Zustand
```javascript
// Zustand
import { create } from 'zustand';

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}));
```

## Accessing State

### Redux
```jsx
// Redux
import { useSelector, useDispatch } from 'react-redux';

function BearCounter() {
  const bears = useSelector((state) => state.bears);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>{bears} bears around here...</h1>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>Add a bear</button>
      <button onClick={() => dispatch({ type: 'RESET' })}>Reset</button>
    </div>
  );
}
```

### Zustand
```jsx
// Zustand
import useStore from './store';

function BearCounter() {
  const bears = useStore((state) => state.bears);
  const increasePopulation = useStore((state) => state.increasePopulation);
  const removeAllBears = useStore((state) => state.removeAllBears);

  return (
    <div>
      <h1>{bears} bears around here...</h1>
      <button onClick={increasePopulation}>Add a bear</button>
      <button onClick={removeAllBears}>Reset</button>
    </div>
  );
}
```

## Using Redux Middleware with Zustand

If you're used to Redux middleware, Zustand provides a way to use Redux-like patterns with its `redux` middleware:

```javascript
import { create } from 'zustand';
import { redux } from 'zustand/middleware';

const useStore = create(
  redux((set) => ({
    bears: 0,
    increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
    removeAllBears: () => set({ bears: 0 }),
  }))
);

// You can now use dispatch if you prefer
const { dispatch } = useStore.getState();
dispatch({ type: 'increasePopulation' });
```

## Benefits of Migrating to Zustand

1. **Simplicity**: Zustand has a simpler API and requires less boilerplate code.
2. **Performance**: With its small bundle size and efficient updates, Zustand can improve your app's performance.
3. **Flexibility**: Zustand allows for more flexible state management patterns.
4. **TypeScript Support**: Zustand has excellent TypeScript support out of the box.
5. **Easy Integration**: You can gradually migrate to Zustand without needing to refactor your entire application at once.

## Conclusion

Migrating from Redux to Zustand can simplify your state management code and potentially improve your application's performance. By understanding the key differences and following the patterns shown in this guide, you can smoothly transition your Redux code to Zustand.

Remember to test your application thoroughly during and after the migration process to ensure that all state management functionality works as expected.