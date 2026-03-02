# State Management Rules

## 🎯 Purpose
Clear rules for when and how to use Zustand for global state and useState for local state. Eliminates the most common React state mistakes.

## 📋 Context
- Zustand for global client state
- TanStack Query for server/async state (see data-fetching-rules.md)
- React useState/useReducer for local UI state

## 💬 The Prompt / Rules

```
You are managing state in a React/Next.js application using Zustand and useState.
Follow these rules for all state management decisions.

**State Decision Tree — Apply In Order**
1. Can this be a URL param or search param? → Put it in the URL
2. Is this fetched from a server? → TanStack Query (not Zustand, not useState)
3. Does only one component use it? → useState in that component
2. Do multiple components (in different trees) need it? → Zustand store
3. Do several related state values update together? → useReducer

**Zustand Store Rules**
- One slice per domain: useUserStore, useCartStore, useUIStore
- File location: store/{domain}.ts
- State + actions in the same store definition
- Actions are plain functions that call set() — not async
- Async operations: call from component or a hook, update store after resolution
- Never store derived values — compute them with selectors

**Zustand Store Structure**
interface UserStore {
  // State
  user: User | null

  // Actions (verbs)
  setUser: (user: User) => void
  clearUser: () => void
}

export const useUserStore = create<UserStore>()((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  clearUser: () => set({ user: null }),
}))

**Zustand Selector Pattern**
// Good — only re-renders when user.name changes
const userName = useUserStore((state) => state.user?.name)

// Bad — re-renders on any store change
const store = useUserStore()

**useState Rules**
- Good for: modal open/close, form field value, tab index, hover state
- Bad for: anything fetched from API, anything shared across components
- Initialize with the final type — not null then set in useEffect
- Group related state into an object: { isLoading, error, data } → useReducer

**What Never Goes in State**
- Values that can be computed from other state (fullName from firstName + lastName)
- Values that only change on page navigation (use URL params)
- Server data (use TanStack Query)
- Refs (use useRef)
- Constants (use module-level const)

**Reset on Logout**
Every Zustand store that holds user-specific data must expose a reset() action.
Call all store resets in your logout handler — order matters (clear auth last).
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{domain}` | Store domain | `user`, `cart`, `notifications` |

## 📝 Example Output

```typescript
// store/cart.ts
import { create } from 'zustand'

interface CartItem {
  id: string
  quantity: number
}

interface CartStore {
  items: CartItem[]
  addItem: (item: CartItem) => void
  removeItem: (id: string) => void
  reset: () => void
}

export const useCartStore = create<CartStore>()((set) => ({
  items: [],
  addItem: (item) => set((state) => ({ items: [...state.items, item] })),
  removeItem: (id) => set((state) => ({ items: state.items.filter((i) => i.id !== id) })),
  reset: () => set({ items: [] }),
}))
```

## 🔄 Variations

- **With Persist:** Add `persist` middleware for cart/preferences that survive page refresh
- **With DevTools:** Add Zustand devtools in development for debugging

---

*Last updated: 2026-03-02*
