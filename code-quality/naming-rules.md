# Naming Rules

## 🎯 Purpose
Consistent naming conventions across files, functions, variables, and types. A codebase where naming is consistent is a codebase where search works.

## 📋 Context
- TypeScript + React project
- Next.js App Router file conventions apply
- Works alongside component-rules.md and typescript-rules.md

## 💬 The Prompt / Rules

```
You are writing code in a TypeScript/React project. Apply these naming conventions consistently.

**Files**
Components:          PascalCase          UserCard.tsx, AuthModal.tsx
Hooks:               camelCase + use     useUserData.ts, useDebounce.ts
Utilities:           camelCase           formatDate.ts, cn.ts
Types:               camelCase           user.types.ts (or user.ts in types/)
Constants:           camelCase           routes.ts, config.ts
Server actions:      camelCase           createUser.ts
API routes:          kebab-case dirs     app/api/user-profile/route.ts
Test files:          same + .test        UserCard.test.tsx

**Variables and Functions**
Variables:           camelCase           const userProfile, let isLoading
Constants (module):  SCREAMING_SNAKE     const MAX_RETRY_COUNT = 3
Functions:           camelCase           function formatCurrency()
Async functions:     camelCase           async function fetchUser()
Event handlers:      handle + Event      function handleSubmit(), handleInputChange
Boolean variables:   is/has/can/should   isLoading, hasError, canEdit, shouldRefetch

**React Components**
Component:           PascalCase          function UserCard()
Component file:      PascalCase          UserCard.tsx
Props interface:     ComponentName+Props  interface UserCardProps
Default export:      page.tsx / layout.tsx only

**TypeScript**
Interfaces:          PascalCase          interface UserProfile
Types:               PascalCase          type UserId = string
Enums / const objs:  PascalCase          const Role = { ADMIN: 'ADMIN' }
Generics:            TName for complex   <TData, TError>

**CSS / Tailwind**
CSS variables:       kebab-case          --color-primary, --font-sans
Custom classes:      kebab-case          .card-header (avoid — prefer Tailwind utilities)

**Database (Prisma)**
Models:              PascalCase singular  model UserProfile
Fields:              camelCase            firstName, createdAt
Enums:               PascalCase           enum UserRole

**Anti-patterns to Avoid**
- No "data" as a variable name (use what it actually contains: users, posts, config)
- No "temp", "tmp", "foo", "bar" in production code
- No abbreviations unless universally understood (id, url, api, db are fine; usr, ctr are not)
- No Hungarian notation (strName, bIsActive, arrItems)
- No overly long names — if it takes more than 4 words, the concept needs a name, not a description
- No single-letter variables outside of loop counters (i, j) and math functions

**Be Precise About What Something Is**
- getUser() returns a User — not fetchUserData()
- createPost() creates a Post — not handleNewPost()
- isAuthenticated — not checkAuth
- userId — not id (when multiple IDs exist in scope)
```

## ⚙️ Variables

None — this file applies universally.

## 📝 Example Output

```typescript
// File: hooks/useUserProfile.ts

const MAX_AVATAR_SIZE = 5 * 1024 * 1024 // 5MB

interface UserProfileState {
  isLoading: boolean
  hasError: boolean
  data: UserProfile | null
}

export function useUserProfile(userId: string) {
  const [state, setState] = useState<UserProfileState>({
    isLoading: true,
    hasError: false,
    data: null,
  })

  async function fetchProfile() { ... }

  return state
}
```

## 🔄 Variations

- **Team Onboarding:** Use this file as the first thing new team members read
- **Code Review:** Use as a checklist — naming violations are easy to catch and fix

---

*Last updated: 2026-03-02*
