---
title: Middleware Usage in Zustand
nav: 5
---

# Middleware Usage in Zustand

Zustand provides a powerful middleware system that allows you to extend and customize the behavior of your stores. This guide will walk you through using built-in middlewares and creating custom ones.

## Using Built-in Middlewares

Zustand comes with several built-in middlewares that you can use to add functionality to your stores.

### persist

The `persist` middleware allows you to persist your store's state to a storage system (e.g., localStorage).

```typescript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface BearState {
  bears: number
  increase: (by: number) => void
}

const useBearStore = create<BearState>()(
  persist(
    (set) => ({
      bears: 0,
      increase: (by) => set((state) => ({ bears: state.bears + by })),
    }),
    { name: 'bear-storage' }
  )
)
```

### devtools

The `devtools` middleware connects your store to the Redux DevTools Extension, allowing you to inspect and debug your state changes.

```typescript
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useBearStore = create<BearState>()(
  devtools(
    (set) => ({
      bears: 0,
      increase: (by) => set((state) => ({ bears: state.bears + by })),
    })
  )
)
```

### immer

The `immer` middleware allows you to write "mutating" updates to your state, which are translated to immutable updates behind the scenes.

```typescript
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

const useBearStore = create<BearState>()(
  immer((set) => ({
    bears: 0,
    increase: (by) => set((state) => { state.bears += by }),
  }))
)
```

## Combining Multiple Middlewares

You can use multiple middlewares together by nesting them:

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

Note: It's recommended to use the `devtools` middleware as the outermost wrapper when combining middlewares.

## Creating Custom Middlewares

You can create custom middlewares to add specific functionality to your stores. Here's a simple example of a logging middleware:

```typescript
import { StateCreator, StoreMutatorIdentifier } from 'zustand'

type Logger = <
  T,
  Mps extends [StoreMutatorIdentifier, unknown][] = [],
  Mcs extends [StoreMutatorIdentifier, unknown][] = [],
>(
  f: StateCreator<T, Mps, Mcs>,
  name?: string
) => StateCreator<T, Mps, Mcs>

const logger: Logger = (f, name) => (set, get, store) => {
  const loggedSet: typeof set = (...a) => {
    set(...a)
    console.log(`${name || 'store'} new state:`, get())
  }
  store.setState = (...a) => {
    store.setState(...a)
    console.log(`${name || 'store'} new state:`, store.getState())
  }
  return f(loggedSet, get, store)
}

// Usage
const useBearStore = create<BearState>()(
  logger(
    (set) => ({
      bears: 0,
      increase: (by) => set((state) => ({ bears: state.bears + by })),
    }),
    'bearStore'
  )
)
```

This custom middleware logs the new state every time it changes.

## TypeScript Considerations

When using middlewares with TypeScript, you may need to provide type information, especially when combining multiple middlewares. Here's an example:

```typescript
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

interface BearState {
  bears: number
  increase: (by: number) => void
}

const useBearStore = create<
  BearState,
  [
    ['zustand/devtools', never],
    ['zustand/persist', BearState]
  ]
>()(
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

This ensures that TypeScript correctly infers the types when using multiple middlewares.

Remember to check the TypeScript section in the main documentation for more detailed information on type inference and advanced usage with middlewares.