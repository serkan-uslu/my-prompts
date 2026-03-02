# my-prompts

> Write your standards once. Use them in every project, every session, forever.

---

## What This Is

A personal library of Claude prompts and coding rules — built for a frontend developer who works across Next.js, Vite, Node.js, and React Native.

Nothing here is theoretical. Every rule was extracted from a real project, a real mistake, or a real pattern worth repeating.

---

## The Problem → The Solution

**Problem:** Every time you start a new project or open a new Claude session, you re-explain the same things. Your preferred stack, naming conventions, architecture patterns, what you hate — all of it, again. Inconsistent output, wasted context, repeated decisions.

**Solution:** Define your standards once in this repo. Copy the relevant file into your project as `CLAUDE.md`, or paste it at the start of a session. Claude works within your rules from the first message.

---

## How to Use

**1. Find the right file**
Browse the structure below and pick the file that matches your current task.

**2. Drop it into your project**
```
your-project/
└── CLAUDE.md  ←  paste the contents of nextjs/development-rules.md here
```
Claude Code reads `CLAUDE.md` automatically on startup. No re-explaining needed.

**3. Or paste it at session start**
Open Claude, paste the file contents. It holds those rules for the entire conversation.

---

## What's Inside

```
my-prompts/
│
├── nextjs/              Next.js 14+ App Router — architecture, API routes, DB, UI
├── vitejs/              Vite + React SPA — project setup, dev rules, build optimization
├── nodejs/              Node.js REST API — Express, security, Prisma, project structure
├── react-native/        Expo + React Native — setup, navigation, platform differences
│
├── styling/             Tailwind CSS conventions, design system, token structure
├── state-and-data/      Zustand, TanStack Query, API client patterns
├── code-quality/        TypeScript, components, naming, debug, review, refactor
├── ai-integration/      Ollama local AI, Anthropic/OpenAI API wrappers
│
├── github/              README templates for different repo types
└── templates/           Blank template — structure for adding new rule files
```

| Section | What it covers |
|---------|---------------|
| `nextjs/development-rules.md` | The base CLAUDE.md for any Next.js project |
| `vitejs/development-rules.md` | The base CLAUDE.md for any Vite SPA |
| `nodejs/security-rules.md` | Non-negotiable security checklist for every Node.js API |
| `code-quality/debug-rules.md` | Structured context template — stops Claude giving generic answers |
| `code-quality/refactor-rules.md` | Safe refactoring rules — behavior must not change |
| `github/readme-rules.md` | System prompts for writing READMEs for any repo type |

---

## Philosophy

- **Create once, reuse everywhere.** The goal isn't a collection of interesting prompts. It's eliminating repeated decisions. If you've explained something to Claude twice, it belongs here.

- **Rules are decisions made once.** Every rule in this library represents a choice — often made the hard way — that shouldn't need to be made again.

- **Specificity over generality.** "Write clean code" is useless. "Named exports for all components, default export only for page.tsx" is actionable. Vague rules produce vague output.

- **Production-tested only.** If a rule hasn't survived contact with a real project, it doesn't belong here. No theoretical best practices.

---

## Adding New Rules

Every file follows the same structure. Use `templates/PROMPT_TEMPLATE.md` as the starting point:

```
🎯 Purpose       — what this file does in 1-2 sentences
📋 Context       — stack versions, assumptions
💬 The Prompt    — the actual rules Claude follows
⚙️ Variables     — what to replace before using
📝 Example       — what correct output looks like
🔄 Variations    — how to adapt for different situations
```

When a project teaches you something new → add it here. When a rule stops working → update it. This library is only useful if it stays current.

---

*Last updated: 2026-03-02*
