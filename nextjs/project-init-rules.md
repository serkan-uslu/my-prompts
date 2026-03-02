# Next.js Project Initialization Rules

## 🎯 Purpose
Guide Claude through scaffolding a new Next.js project with all conventions set up from day one, so you don't accumulate tech debt from the start.

## 📋 Context
- Creating a brand new Next.js 14+ project
- Want consistent structure before writing any feature code
- Stack: Next.js + TypeScript + Tailwind + Prisma/Supabase + Zustand + TanStack Query

## 💬 The Prompt / Rules

```
You are setting up a new Next.js 14+ project called {project_name}.
Follow these initialization rules strictly. Do not add any feature code yet — only the scaffold.

**Project Creation**
Use: npx create-next-app@latest {project_name} --typescript --tailwind --eslint --app --src-dir=false --import-alias="@/*"

**Required Directory Structure**
Create these directories with .gitkeep files if empty:
app/              — routes only
components/       — shared UI components
  ui/             — primitive components (Button, Input, Modal)
  layout/         — structural components (Header, Sidebar, Footer)
lib/              — pure utilities, no side effects
hooks/            — custom React hooks (use* prefix)
types/            — shared TypeScript types
server/           — server-only code
  db/             — database queries
  actions/        — Server Actions
constants/        — app-wide constants

**Required Dependencies**
Install immediately:
- clsx tailwind-merge — class utilities
- zustand — global state
- @tanstack/react-query — server state
- zod — validation
- lucide-react — icons

**lib/utils.ts — Create First**
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

**TypeScript Config**
Ensure tsconfig.json has:
- "strict": true
- "noUncheckedIndexedAccess": true
- paths: "@/*" → "./*"

**ESLint Config**
Add to .eslintrc.json:
- no-unused-vars: error
- no-console: warn (allow in server code with comment)

**Environment Variables**
Create .env.local with placeholder sections:
# Database
DATABASE_URL=

# Auth
NEXTAUTH_SECRET=
NEXTAUTH_URL=http://localhost:3000

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000

Create .env.example with same keys but no values.
Add .env.local to .gitignore (verify it's there).

**Initial app/ Structure**
app/
  layout.tsx       — root layout with Providers wrapper
  page.tsx         — minimal home page
  globals.css      — Tailwind directives only
  providers.tsx    — QueryClientProvider + any other providers

**Git Setup**
- Initialize with meaningful .gitignore (Next.js template)
- First commit: "chore: initial Next.js project setup"
- Never commit .env.local

After setup, list what was created and confirm what still needs to be configured (auth, database connection, etc.).
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{project_name}` | Name of the project | `saas-dashboard` |
| `{auth_provider}` | Auth solution | `NextAuth.js` / `Clerk` |
| `{db_provider}` | Database | `PostgreSQL` / `Supabase` |

## 📝 Example Output

After running, you should have:
```
my-app/
├── app/layout.tsx      ← Providers wrapper set up
├── app/page.tsx        ← Clean placeholder
├── components/ui/      ← Empty, ready for components
├── lib/utils.ts        ← cn() utility ready
├── types/index.ts      ← Empty exports file
├── .env.local          ← Placeholders only
└── .env.example        ← Committed to repo
```

## 🔄 Variations

- **Static Site:** Skip Zustand, TanStack Query, database sections
- **With Clerk Auth:** Add `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` and `CLERK_SECRET_KEY` to env section
- **Supabase:** Add `NEXT_PUBLIC_SUPABASE_URL` and `NEXT_PUBLIC_SUPABASE_ANON_KEY`

---

*Last updated: 2026-03-02*
