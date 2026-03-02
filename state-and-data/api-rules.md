# API Client Rules

## 🎯 Purpose
Conventions for the API client layer — how to structure fetch calls, handle errors, and type responses consistently. Eliminates inconsistent error handling across the codebase.

## 📋 Context
- Next.js project consuming REST or third-party APIs
- TypeScript strict mode
- TanStack Query handles caching (see data-fetching-rules.md)

## 💬 The Prompt / Rules

```
You are building the API client layer for a Next.js application.
Follow these rules for all fetch/API code.

**API Client Structure**
All API calls go through a central client in lib/api/:
lib/api/client.ts     — base fetch wrapper
lib/api/users.ts      — user API functions
lib/api/posts.ts      — posts API functions

Never call fetch() directly in a component or hook — always go through the API layer.

**Base Client Pattern**
The base client handles:
- Base URL prefix
- Auth headers (read from session/store)
- Content-Type header
- Consistent error handling
- Response parsing

async function apiClient<T>(endpoint: string, options?: RequestInit): Promise<T> {
  const response = await fetch(`${process.env.NEXT_PUBLIC_API_URL}${endpoint}`, {
    headers: {
      'Content-Type': 'application/json',
      ...getAuthHeader(),
    },
    ...options,
  })

  if (!response.ok) {
    throw new ApiError(response.status, await response.json())
  }

  return response.json()
}

**Error Handling**
- Create a custom ApiError class: new ApiError(status, message, details?)
- Throw on non-2xx responses — don't return error objects
- TanStack Query catches thrown errors and puts them in the error state
- 401 errors: redirect to login (handle in a global error boundary or Query config)
- 422 errors: return validation errors to the form

**Response Types**
- Type every API response — no unknown or any return types
- Use Zod to validate external API responses at the boundary:
  const user = UserSchema.parse(await response.json())
- For internal APIs you control, trust your own types (skip runtime validation)

**Naming Conventions**
API functions are named by action:
- getUser(id) — fetch one
- getUsers(params) — fetch list
- createUser(data) — create
- updateUser(id, data) — full update
- patchUser(id, partial) — partial update
- deleteUser(id) — delete

**Caching and Freshness**
- For Next.js Server Components: use fetch() with cache/revalidate options
  fetch(url, { next: { revalidate: 60 } })   — revalidate every 60s
  fetch(url, { cache: 'no-store' })           — always fresh
- For client-side: use TanStack Query (see data-fetching-rules.md)
- Never manually cache API responses in state

**Authentication Headers**
- Never hardcode auth tokens
- Read from: cookies (server), session (NextAuth), or Zustand store (client)
- Don't pass the token as a URL parameter — use Authorization header
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{api_base}` | Base API URL | `https://api.example.com/v1` |
| `{resource}` | Resource name | `users`, `posts` |

## 📝 Example Output

```typescript
// lib/api/users.ts
import { apiClient } from './client'
import type { User } from '@/types'

export async function getUser(id: string): Promise<User> {
  return apiClient<User>(`/users/${id}`)
}

export async function createUser(data: CreateUserInput): Promise<User> {
  return apiClient<User>('/users', {
    method: 'POST',
    body: JSON.stringify(data),
  })
}
```

## 🔄 Variations

- **Third-party APIs:** Add runtime Zod validation for responses you don't control
- **GraphQL:** Replace REST patterns with a typed GraphQL client (urql or Apollo)

---

*Last updated: 2026-03-02*
