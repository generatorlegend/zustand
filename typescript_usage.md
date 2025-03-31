---
title: TypeScript Usage Guide
nav: 8
---

# TypeScript Usage Guide

This guide covers how to use Zustand with TypeScript, including creating typed stores, leveraging type inference, and common patterns for maintaining type safety.

## Basic Usage

When using Zustand with TypeScript, you need to provide type annotations for your store's state and actions. Here's a basic example:

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

Note the use of `create<BearState>()` with an extra set of parentheses. This is necessary for proper type inference.

## Type Inference with `combine`

If you prefer to avoid explicitly typing your entire state, you can use the `combine` middleware, which infers types from your initial state:

```typescript
import { create } from 'zustand'
import { combine } from 'zustand/middleware'

const useBearStore = create(
  combine({ bears: 0 }, (set) => ({
    increase: (by: number) => set((state) => ({ bears: state.bears + by })),
  }))
)
```

## Extracting State Type

To reuse your store's state type elsewhere in your code, you can use the `ExtractState` type helper:

```typescript
import { create, ExtractState } from 'zustand'

const useBearStore = create(/* ... */)

type BearState = ExtractState<typeof useBearStore>
```

## Using Middlewares

Middlewares can be used with TypeScript without any special considerations. Here's an example using `devtools` and `persist` middlewares:

```typescript
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

interface BearState {
  bears: number
  increase: (by: number) => void
}

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

## Slices Pattern

For larger stores, you can use the slices pattern to organize your state and actions:

```typescript
import { create, StateCreator } from 'zustand'

interface BearSlice {
  bears: number
  addBear: () => void
}

interface FishSlice {
  fishes: number
  addFish: () => void
}

const createBearSlice: StateCreator<
  BearSlice & FishSlice,
  [],
  [],
  BearSlice
> = (set) => ({
  bears: 0,
  addBear: () => set((state) => ({ bears: state.bears + 1 })),
})

const createFishSlice: StateCreator<
  BearSlice & FishSlice,
  [],
  [],
  FishSlice
> = (set) => ({
  fishes: 0,
  addFish: () => set((state) => ({ fishes: state.fishes + 1 })),
})

const useBoundStore = create<BearSlice & FishSlice>()((...a) => ({
  ...createBearSlice(...a),
  ...createFishSlice(...a),
}))
```

## Advanced Types for Middleware Authors

If you're writing custom middleware that modifies the store type, you'll need to use more advanced TypeScript features. Here's a basic example:

```typescript
import { StateCreator, StoreMutatorIdentifier } from 'zustand'

type MyMiddleware = <
  T,
  Mps extends [StoreMutatorIdentifier, unknown][] = [],
  Mcs extends [StoreMutatorIdentifier, unknown][] = []
>(
  f: StateCreator<T, Mps, Mcs>,
  options: MyMiddlewareOptions
) => StateCreator<T, Mps, [['myMiddleware', MyMiddlewareOptions], ...Mcs]>

// Implement your middleware...
```

For more details on authoring middlewares and handling complex type scenarios, refer to the [TypeScript guide in the official documentation](https://github.com/pmndrs/zustand/blob/main/docs/guides/typescript.md#authoring-middlewares-and-advanced-usage).

## Conclusion

This guide covers the basics of using Zustand with TypeScript. Remember to leverage type inference where possible, and use explicit type annotations when needed for clarity and type safety. For more advanced usage and edge cases, consult the official Zustand documentation.