# my-prompts — Personal Prompt Library

> Prompt engineering is now a core skill for Frontend Developers.
> This repo is a collection of prompts and rules tested in production with AI tools.

---

## What This Is

A living collection of Claude prompts and rules that make AI actually useful in day-to-day frontend development. Not theoretical — every rule here has been used in a real project.

**The core idea:** Stop re-explaining context every session. Define your standards once, reuse everywhere.

---

## How to Use

### Option 1 — As CLAUDE.md (Recommended)
Copy any rules file into your project as `CLAUDE.md`. Claude Code reads it automatically at startup.

```
my-project/
└── CLAUDE.md   ← paste contents of nextjs/development-rules.md here
```

### Option 2 — Paste at Session Start
Open a new Claude chat, paste the relevant rules file. Claude follows those rules for the entire session.

### Option 3 — Combine Rules
For a full-stack Next.js project, combine:
```
nextjs/development-rules.md
+
code-quality/typescript-rules.md
+
state-and-data/api-rules.md
```

---

## Repository Structure

```
my-prompts/
│
├── README.md
├── PHILOSOPHY.md
├── CHANGELOG.md
│
├── nextjs/              # Next.js 14+ App Router rules
├── react-native/        # Expo + React Native rules
├── styling/             # Tailwind CSS + Design System
├── code-quality/        # TypeScript, components, debugging
├── state-and-data/      # State management, API, data fetching
├── ai-integration/      # Ollama, AI API wrappers
└── templates/           # Prompt file template
```

---

## Priority Guide

Start here for immediate value:

| File | When to Use |
|------|-------------|
| `nextjs/development-rules.md` | Every Next.js project |
| `code-quality/component-rules.md` | Framework-agnostic, universal |
| `nextjs/feature-rules.md` | When building a new feature |
| `code-quality/debug-rules.md` | When debugging a tricky bug |

---

## GitHub Topics

`prompt-engineering` `nextjs` `claude` `ai` `developer-tools` `frontend` `react-native` `typescript`

---

*Last updated: 2026-03-02*
