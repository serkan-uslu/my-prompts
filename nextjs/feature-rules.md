# Next.js Feature Development Rules

## 🎯 Purpose
A systematic prompt for building a complete new feature in Next.js — from route to database, with tests. Prevents the "I'll add validation later" trap.

## 📋 Context
- Feature involves a new route, UI, data layer, and possibly a Server Action
- Existing project follows `development-rules.md` conventions
- Want complete, production-ready implementation — not a prototype

## 💬 The Prompt / Rules

```
You are building the feature: {feature_name} for a Next.js 14+ App Router project.
Build it completely and in the correct order. Do not skip steps.

**Build Order — Follow This Sequence**
1. Types first — define all TypeScript types and interfaces
2. Database layer — schema changes, then query functions
3. Server Actions or API routes — business logic with validation
4. Server Components — data fetching, layout
5. Client Components — interactivity only where needed
6. Loading and error states — loading.tsx and error.tsx
7. Validation — Zod schemas matching the database types

**Types (Step 1)**
Define in types/{feature}.ts:
- Entity type matching the database model
- Form/input types (often Omit of the entity)
- API response types
- Component prop types

**Database Layer (Step 2)**
In server/db/{feature}.ts:
- One function per operation (getUser, createUser, updateUser)
- Functions are server-only — import from 'server-only' package
- Return plain objects, not Prisma types — transform at the boundary
- Always handle not-found cases explicitly

**Validation (Step 3)**
In lib/validations/{feature}.ts:
- Zod schema for every form or mutation
- Schema name matches the action: CreateUserSchema, UpdateUserSchema
- Reuse schemas between Server Action validation and client form validation

**Server Actions (Step 4)**
In server/actions/{feature}.ts:
- "use server" directive at the top
- Validate input with Zod before any DB call
- Return { success: true, data } or { success: false, error: string }
- Never throw — always return structured errors
- Revalidate relevant paths after mutations

**UI — Server Components First (Step 5)**
- Page component fetches data server-side
- Pass data as props to child components
- Mark "use client" only when adding event handlers or browser state

**UI — Loading & Error (Step 6)**
- Add loading.tsx for any route that fetches data
- Add error.tsx with retry button
- Use Suspense boundaries for partial loading states

**Forms**
- Use react-hook-form + zodResolver for client forms
- Disable submit button during pending state
- Show field-level errors, not just toast
- Optimistic UI for list mutations when appropriate

When you finish each step, say "Step N complete" and wait for confirmation before proceeding.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{feature_name}` | The feature being built | `User Profile Management` |
| `{entity_name}` | Main data entity | `UserProfile` |
| `{route_path}` | App Router path | `/dashboard/profile` |

## 📝 Example Output

For "Add User Comments feature":
```typescript
// Step 1 — types/comment.ts
export interface Comment {
  id: string
  content: string
  authorId: string
  postId: string
  createdAt: Date
}
export type CreateCommentInput = Pick<Comment, 'content' | 'postId'>

// Step 3 — lib/validations/comment.ts
export const CreateCommentSchema = z.object({
  content: z.string().min(1).max(1000),
  postId: z.string().cuid(),
})
```

## 🔄 Variations

- **Read-only feature:** Skip Server Actions, only build DB queries + Server Components
- **Real-time feature:** Add notes about Supabase Realtime or polling strategy
- **Admin feature:** Add authorization check before every Server Action

---

*Last updated: 2026-03-02*
