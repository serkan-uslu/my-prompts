# my-prompts

> My personal AI system prompts and coding rules — for my own workflow and projects.

---

## What This Is

My personal collection of system prompts, task prompts, and coding rules. I use these in my own projects and AI sessions — Claude, GPT, and Gemini.

Instead of re-explaining my stack, preferences, and conventions to an AI every single session, I write them here once and reuse them everywhere. Every file here comes from a real project or a real mistake.

---

## How I Use These

**Drop into a project as `CLAUDE.md`**
```
my-project/
└── CLAUDE.md  ←  nextjs/development-rules.md content goes here
```
Claude Code reads it automatically on startup. No re-explaining needed.

**Or paste at session start** — works the same with Claude, ChatGPT, and Gemini.

---

## Prompt Types in This Repo

| Type | What it is | How to use |
|------|-----------|------------|
| **System / Rules** | Defines the AI's behavior for an entire session | Paste once at the start or use as CLAUDE.md |
| **Task** | One-shot prompt for a specific output | Fill in the variables, send, get the result |
| **Chain** | Multi-step sequence — each step waits for your input | Follow the numbered steps, confirm between each |
| **Image Generation** | JSON payload for Flux.1 Dev | Copy the JSON, adjust colors/description, run |

---

## System / Rules Prompts

Set the AI's identity and constraints for a whole session. Model-agnostic — works with Claude, GPT, Gemini.

### Next.js

| File | What it does |
|------|-------------|
| [nextjs/development-rules.md](nextjs/development-rules.md) | Core rules for any Next.js 14+ project — architecture, file structure, Server vs Client components, state management. The base CLAUDE.md for every Next.js project. |
| [nextjs/project-init-rules.md](nextjs/project-init-rules.md) | Scaffold a new Next.js project from zero — directory structure, required dependencies, env setup, first commit. Run this before writing any feature code. |
| [nextjs/feature-rules.md](nextjs/feature-rules.md) | Build a complete feature in the correct order: types → DB → server action → UI → loading/error states. Prevents "I'll add validation later" debt. |
| [nextjs/api-rules.md](nextjs/api-rules.md) | API route conventions — when to use route handlers vs Server Actions, response format, auth checks, webhook patterns. |
| [nextjs/db-rules.md](nextjs/db-rules.md) | Prisma + Supabase rules — schema conventions, query patterns, migrations, soft deletes, N+1 prevention. |
| [nextjs/ui-rules.md](nextjs/ui-rules.md) | UI patterns for Next.js — Server vs Client component decisions for UI, next/image, next/font, shadcn/ui, accessibility basics. |

### Vite.js

| File | What it does |
|------|-------------|
| [vitejs/development-rules.md](vitejs/development-rules.md) | Core rules for Vite + React SPA — feature-based architecture, React Router v6, SPA auth pattern, env variables. The base CLAUDE.md for dashboards and admin panels. |
| [vitejs/project-init-rules.md](vitejs/project-init-rules.md) | Scaffold a new Vite + React + TypeScript project — path aliases, directory structure, TanStack Query setup, env vars. |
| [vitejs/build-rules.md](vitejs/build-rules.md) | Bundle optimization — code splitting, manualChunks, bundle analysis with visualizer, SPA deployment config (Netlify, Vercel, Nginx). |

### Node.js

| File | What it does |
|------|-------------|
| [nodejs/api-rules.md](nodejs/api-rules.md) | Express REST API — Controller/Service/Repository pattern, response format standard, Zod validation middleware, pagination, error handler. |
| [nodejs/security-rules.md](nodejs/security-rules.md) | Non-negotiable security checklist — helmet, CORS, rate limiting, JWT auth, authorization per endpoint, input validation, production checklist. |
| [nodejs/db-rules.md](nodejs/db-rules.md) | Prisma + Node.js — Repository pattern, transactions, N+1 prevention, soft delete middleware, cursor-based pagination, migration workflow. |
| [nodejs/project-init-rules.md](nodejs/project-init-rules.md) | Scaffold a new Node.js + TypeScript + Express project — tsconfig, Zod-validated env config, directory structure, npm scripts. |

### React Native

| File | What it does |
|------|-------------|
| [react-native/expo-setup-rules.md](react-native/expo-setup-rules.md) | Expo + React Native project rules — Expo Router navigation, NativeWind styling, FlatList patterns, platform differences from web, EAS build setup. |

### Code Quality

| File | What it does |
|------|-------------|
| [code-quality/component-rules.md](code-quality/component-rules.md) | Framework-agnostic React component rules — structure, props design, state decisions, useEffect discipline, memoization. Works for Next.js and React Native. |
| [code-quality/typescript-rules.md](code-quality/typescript-rules.md) | TypeScript strict mode rules — no-any policy, type vs interface, utility types, null handling, enums vs const objects. |
| [code-quality/naming-rules.md](code-quality/naming-rules.md) | Naming conventions for files, variables, functions, types, DB models — a searchable, consistent codebase starts here. |
| [code-quality/debug-rules.md](code-quality/debug-rules.md) | Structured debug context template — fill in expected/actual/error/tried, stops AI from giving generic advice. |
| [code-quality/refactor-rules.md](code-quality/refactor-rules.md) | Safe refactoring rules — behavior must not change, bugs are reported not fixed, one change type at a time. |
| [code-quality/code-review-rules.md](code-quality/code-review-rules.md) | Code review prompt — structures feedback into Critical / Important / Suggestions / What's good. |

### Styling & State

| File | What it does |
|------|-------------|
| [styling/tailwind-rules.md](styling/tailwind-rules.md) | Tailwind CSS conventions — class ordering, cn() for conditionals, responsive pattern, color semantics, dark mode, when to extract components. |
| [styling/design-system-rules.md](styling/design-system-rules.md) | Design token structure, semantic color system, typography scale, shadcn/ui composition rules. |
| [state-and-data/state-management-rules.md](state-and-data/state-management-rules.md) | State decision tree — URL vs TanStack Query vs Zustand vs useState. Zustand store structure, selector pattern, reset on logout. |
| [state-and-data/data-fetching-rules.md](state-and-data/data-fetching-rules.md) | TanStack Query v5 — query key conventions, stale time guidelines, mutation + invalidation, optimistic updates. |
| [state-and-data/api-rules.md](state-and-data/api-rules.md) | API client layer — base fetch wrapper, error handling with custom ApiError, naming conventions, caching strategies. |

### AI Integration

| File | What it does |
|------|-------------|
| [ai-integration/api-wrapper-rules.md](ai-integration/api-wrapper-rules.md) | Integrating Claude/OpenAI into production apps — security, cost control, streaming with Vercel AI SDK, system prompt management, monitoring. |
| [ai-integration/ollama-prompts.md](ai-integration/ollama-prompts.md) | Local AI with Ollama — developer workflow prompts, Next.js integration with streaming, provider-switching pattern. |

---

## Task Prompts

One-shot prompts for a specific output. Fill in the variables, send, done.

| File | What it does |
|------|-------------|
| [task-prompts/commit-message.md](task-prompts/commit-message.md) | Writes a Conventional Commit message from a git diff or change description. Includes body when needed, skips it when not. |
| [task-prompts/pr-description.md](task-prompts/pr-description.md) | Writes a GitHub PR description with What / Why / Changes / How to Test. Structured for reviewers who have no context. |
| [task-prompts/feature-spec.md](task-prompts/feature-spec.md) | Turns a vague feature request into a technical spec — user flow, technical breakdown, edge cases, open questions, complexity estimate. |
| [task-prompts/code-explanation.md](task-prompts/code-explanation.md) | Explains code at three levels: junior (plain language + analogy), peer (code review prep), rubber duck (line-by-line for debugging). |

---

## Chain Prompts

Multi-step sequences. The AI stops after each step and waits for your input before continuing.

| File | What it does |
|------|-------------|
| [chain-prompts/feature-planning.md](chain-prompts/feature-planning.md) | 3-step chain: analyze the feature → write the implementation plan → implement one step at a time. Keeps you in control of a complex build. |
| [chain-prompts/bug-investigation.md](chain-prompts/bug-investigation.md) | 3-step chain: hypothesis → minimal fix → regression risk. Stops the AI from throwing 10 suggestions at you before understanding the problem. |

---

## Image Generation

JSON payloads ready to paste. Flux.1 Dev via fal.ai or Replicate.

| File | What it does |
|------|-------------|
| [image-generation/flux2-dev-prompts.md](image-generation/flux2-dev-prompts.md) | 5 categories: UI mockup (dark/light), landing page hero, OG/social media card, app icon, hero illustration. Each as a ready-to-run JSON payload with a parameters reference and Flux-specific writing tips. |

---

## GitHub

| File | What it does |
|------|-------------|
| [github/readme-rules.md](github/readme-rules.md) | System prompts for writing GitHub READMEs — three templates: Code repo, Content repo, Boilerplate. Plus universal writing rules that apply to all. |

---

## Adding New Rules

Every file follows the same structure. Use [`templates/PROMPT_TEMPLATE.md`](templates/PROMPT_TEMPLATE.md) as the starting point.

When a project teaches me something new → I add it here. When a rule stops working → I update it.

---

## Philosophy

- **Create once, reuse everywhere.** If I've explained something to an AI twice, it belongs here.
- **Rules are decisions made once.** Every rule represents a choice — often made the hard way — that shouldn't need to be made again.
- **Specificity over generality.** "Write clean code" is useless. "Named exports for all components, default export only for page.tsx" is actionable.
- **Production-tested only.** If a rule hasn't survived contact with a real project, it doesn't belong here.

---

*Last updated: 2026-03-02*
