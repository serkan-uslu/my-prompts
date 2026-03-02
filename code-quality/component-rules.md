# React Component Rules

## 🎯 Purpose
Framework-agnostic rules for writing React components. Applies to both Next.js and React Native projects. Focuses on structure, prop design, and avoiding common pitfalls.

## 📋 Context
- React 18+ functional components
- TypeScript strict mode
- Works with Next.js and React Native

## 💬 The Prompt / Rules

```
You are writing React components. Follow these rules for every component you create or modify.

**Component Structure (in this order)**
1. Imports
2. Types/interfaces for props
3. Component function
4. Helper functions used only by this component (or move to lib/ if reusable)
5. Styles (if CSS modules or styled-components)

**Component Size**
- If a component exceeds 150 lines, consider splitting it
- If a component does two unrelated things, split it — don't fix it with more state
- One component per file — no two exported components in the same file (except test helpers)

**Props Design**
- Destructure props in the function signature
- Define a named interface for props (not inline type)
- Required props first, optional props after
- Don't pass an entire object when you only need two fields — pick the fields
- Boolean props: use isLoading, not loading. Use hasError, not error (when error is boolean)
- Callback props: use on prefix — onSubmit, onChange, onClose
- Don't use prop drilling past 2 levels — use context or state management

**State Management in Components**
- useState for simple, local, UI-only state
- useReducer when you have 3+ related state fields that update together
- Never useState for values that can be computed from other state/props
- Initialize state with the actual starting value — never null then immediately set in useEffect

**useEffect Rules**
- Every useEffect must have a comment explaining why it exists
- If you're fetching data in useEffect, you probably should use TanStack Query instead
- No async functions directly in useEffect — create an inner async function
- Cleanup subscriptions, timers, and event listeners in the return function

**Event Handlers**
- Name handlers: handle + Event — handleSubmit, handleInputChange
- Extract complex handler logic into a named function — not inline arrow functions in JSX
- Don't re-create handlers on every render — use useCallback when passing to memoized children

**Conditional Rendering**
- Use ternary for simple if/else: {isLoading ? <Skeleton /> : <Content />}
- Use && only when the false case renders nothing and there's no risk of 0 rendering
- Extract complex conditions into a named boolean: const showEmptyState = !isLoading && items.length === 0

**Lists**
- Always use a stable key — never array index as key for mutable lists
- If items don't have IDs, generate them with crypto.randomUUID() when creating

**Memoization**
- Don't memoize by default — measure first
- React.memo: only when the component re-renders visibly slow with same props
- useMemo: only for expensive computations (not simple object creation)
- useCallback: only when passing callbacks to memoized children

**No Magic**
- No clever one-liners that require knowing 3 JavaScript tricks to understand
- Readable beats clever — future you will thank present you
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{component_name}` | Component being built | `UserProfileCard` |
| `{context}` | React or React Native | `Next.js web` / `Expo mobile` |

## 📝 Example Output

```typescript
interface UserCardProps {
  userId: string
  name: string
  avatarUrl?: string
  onSelect: (userId: string) => void
}

export function UserCard({ userId, name, avatarUrl, onSelect }: UserCardProps) {
  function handleClick() {
    onSelect(userId)
  }

  return (
    <button onClick={handleClick} className="...">
      {avatarUrl && <img src={avatarUrl} alt={name} />}
      <span>{name}</span>
    </button>
  )
}
```

## 🔄 Variations

- **React Native:** Replace className with StyleSheet, adjust event handler names
- **With Context:** Add notes on when to reach for createContext vs prop drilling vs Zustand

---

*Last updated: 2026-03-02*
