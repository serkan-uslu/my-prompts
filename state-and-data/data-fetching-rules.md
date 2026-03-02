# Data Fetching Rules (TanStack Query)

## 🎯 Purpose
Patterns for using TanStack Query (React Query) correctly. Covers query keys, mutations, optimistic updates, and avoiding the most common misuse patterns.

## 📋 Context
- TanStack Query v5
- Next.js 14+ with App Router (mix of Server Components and client-side queries)
- Pairs with api-rules.md for the actual fetch functions

## 💬 The Prompt / Rules

```
You are using TanStack Query v5 for client-side data fetching in a Next.js application.
Follow these rules for all query and mutation code.

**When to Use TanStack Query**
Use TanStack Query for data that:
- Is fetched by a Client Component
- Needs to stay fresh (polling, refetch on focus)
- Benefits from caching across components
- Requires loading/error/success states in the client

Use Server Components + fetch() for data that:
- Can be fetched at request time server-side
- Doesn't need to stay live after initial render
- Doesn't depend on client-side state

**Query Key Convention**
Query keys are arrays — they determine cache identity and invalidation:

// Pattern: [resource, scope, identifier]
['users']                          — all users
['users', 'list', { page: 1 }]    — paginated list
['users', 'detail', userId]        — single user
['posts', 'by-user', userId]       — posts by user

Put query keys in a constant file: lib/query-keys.ts
Never construct query keys inline in components — import from the constant file.

**useQuery Pattern**
const { data, isLoading, error } = useQuery({
  queryKey: queryKeys.user(userId),
  queryFn: () => getUser(userId),
  enabled: !!userId,              // only run when we have an id
  staleTime: 5 * 60 * 1000,      // 5 minutes
})

Always handle all three states: isLoading, error, and data.

**Stale Time Guidelines**
- User profile: 5 minutes — doesn't change often
- List data: 1-2 minutes — moderate freshness
- Real-time data: 0 (default) — always fresh
- Config/static data: Infinity — never refetch

**useMutation Pattern**
const mutation = useMutation({
  mutationFn: (data: CreateUserInput) => createUser(data),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: queryKeys.users() })
    toast.success('User created')
  },
  onError: (error) => {
    toast.error(error.message)
  },
})

**Invalidation After Mutations**
- After create: invalidate the list query
- After update: invalidate both the detail query and the list query
- After delete: invalidate the list query, remove the detail query

Use invalidateQueries — not manually setting cache — unless doing optimistic updates.

**Optimistic Updates**
Only use optimistic updates when:
- The mutation is highly likely to succeed
- The user experience benefit justifies the complexity

If using optimistic updates, always handle rollback in onError.

**Prefetching**
- Prefetch on hover for navigation links
- Prefetch in Server Components with dehydrate/HydrationBoundary for initial page data
- Don't prefetch everything — profile first

**Infinite Queries**
- Use useInfiniteQuery for infinite scroll lists
- Use cursor-based pagination, not offset (more reliable)
- Return { data, nextCursor } from the API
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{resource}` | Data resource | `users`, `posts` |
| `{stale_time}` | Appropriate stale time | `5 * 60 * 1000` |

## 📝 Example Output

```typescript
// lib/query-keys.ts
export const queryKeys = {
  users: () => ['users'] as const,
  userDetail: (id: string) => ['users', 'detail', id] as const,
  userPosts: (userId: string) => ['posts', 'by-user', userId] as const,
}

// hooks/useUser.ts
export function useUser(userId: string) {
  return useQuery({
    queryKey: queryKeys.userDetail(userId),
    queryFn: () => getUser(userId),
    enabled: !!userId,
    staleTime: 5 * 60 * 1000,
  })
}
```

## 🔄 Variations

- **With Suspense:** Use `useSuspenseQuery` and wrap in `<Suspense>` for cleaner loading states
- **Server Prefetch:** Add Server Component prefetching pattern with `HydrationBoundary`

---

*Last updated: 2026-03-02*
