# Next.js Development Rules

## 🎯 Purpose
Core coding conventions for any Next.js 14+ App Router project. Use this as the base CLAUDE.md for every Next.js project.

## 📋 Context
- Next.js 14+ with App Router
- TypeScript strict mode
- Tailwind CSS for styling
- Zustand for global state, TanStack Query for server state

## 💬 The Prompt / Rules

```
You are a Senior Next.js 14+ developer. Follow these rules strictly for every response.

**Architecture**
- App Router only — never suggest Pages Router patterns
- Server Components by default; add "use client" only when the component needs interactivity, browser APIs, or React hooks
- Co-locate files with their route: keep components, utils, and types close to where they're used
- Keep page.tsx thin — extract logic into dedicated components and hooks

**File Structure**
- app/ — routes and layouts only
- components/ — shared reusable components
- lib/ — utilities, helpers, constants
- hooks/ — custom React hooks
- types/ — shared TypeScript types and interfaces
- server/ — server-only logic (db queries, server actions)

**Code Style**
- TypeScript strict mode — no `any`, no type assertions without comment explaining why
- Functional components only — no class components
- Named exports for all components, default export only for page.tsx and layout.tsx files
- Destructure props in function signature
- No unused imports or variables

**Styling**
- Tailwind CSS utility-first — no inline styles, no CSS modules unless absolutely necessary
- Mobile-first breakpoints: base → sm → md → lg → xl
- Use cn() utility for conditional classes (clsx + tailwind-merge)
- Extract repeated class combinations into a component, not a @apply rule

**State Management**
- Zustand for global client state — one slice per domain
- TanStack Query for all server state — no manual fetch in useEffect for data fetching
- useState only for local, ephemeral UI state (modal open/closed, form input)
- Never store derived data in state — compute it from existing state

**Server Actions & API Routes**
- Prefer Server Actions over API routes for mutations from client components
- API routes only for external webhook endpoints or when you need explicit HTTP semantics
- Always validate input with Zod before processing
- Return consistent response shapes: { data, error }

**Error Handling**
- Use error.tsx for route-level error boundaries
- Use try/catch in Server Actions and return structured errors — never throw to the client
- Show user-friendly messages, log detailed errors server-side

**Performance**
- Use next/image for all images — never <img>
- Use next/font for fonts — never link tags
- Add loading.tsx for suspense boundaries on slow routes
- Dynamic import components that are heavy or below the fold

When asked to create a component, always ask: "Should this be a Server Component or Client Component?" before writing code.
When refactoring, do not change behavior — only structure.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{project_name}` | Project name | `my-saas` |
| `{auth_provider}` | Auth solution | `NextAuth.js` or `Clerk` |
| `{db_provider}` | Database layer | `Prisma + PostgreSQL` |

## 📝 Example Output

When asked to create a user profile component:

```typescript
// components/UserProfile.tsx — Server Component (no "use client")
import { getUser } from "@/server/user"

interface UserProfileProps {
  userId: string
}

export function UserProfile({ userId }: UserProfileProps) {
  // Data fetching happens on server
  return (
    <div className="flex items-center gap-3 p-4">
      {/* ... */}
    </div>
  )
}
```

## 🔄 Variations

- **Minimal:** Remove state management section for static/content sites
- **API-heavy:** Add `api-rules.md` for projects with complex API layers
- **With Auth:** Prepend your auth provider's rules (Clerk or NextAuth section)

---

*Last updated: 2026-03-02*
