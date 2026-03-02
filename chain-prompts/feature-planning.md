# Chain Prompt — Feature Planning

## 🎯 Purpose
Büyük bir feature'ı aşamalı olarak planlamak ve implement etmek için. Her adım onaylanmadan bir sonrakine geçilmez — AI seni doğru yöne götürürken kontrolü sen tutarsın.

## 📋 Context
- 3 adımlı chain: Analiz → Plan → Implement (adım adım)
- Her adım arasında onay veriyorsun
- Claude, GPT, Gemini hepsinde çalışır

## 💬 The Chain

### STEP 1 — Analysis (Send First)
```
Analyze the following feature request. Do NOT write any code yet.

Feature: {feature description}
Stack: {tech stack}

Tell me:
1. What needs to be built — list every piece (UI, data model, business logic, API)
2. What assumptions you're making (things not specified in the request)
3. What risks or tricky parts exist
4. What questions, if answered, would change the implementation significantly

Stop here and wait for my response.
```

### STEP 2 — Implementation Plan (Send After Reviewing Step 1)
```
{Your feedback on the analysis — confirm assumptions, answer questions, flag disagreements}

Now write the implementation plan. Order the steps so each one builds on the previous.
For each step: what file/function to create or modify, and what it should do.
Do NOT write actual code yet — just the plan.

Stop after the plan and wait.
```

### STEP 3 — Implement One Step at a Time (Repeat Per Step)
```
The plan looks good. Now implement Step {N} only.

Rules:
- Write complete, working code — not pseudocode, not placeholders
- Follow the project's existing conventions (use the same patterns you see in the codebase)
- If you realize Step {N} requires something from a later step, say so before writing code
- When done, stop and tell me what Step {N+1} will require from me before you can start it
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{feature}` | Feature açıklaması | "Team invitation via email" |
| `{stack}` | Tech stack | `Next.js 14, Prisma, Resend` |
| `{N}` | Adım numarası | `1`, `2`, `3` |

## 📝 Example Flow

**You → Step 1:**
> Feature: Users can export their dashboard data as a CSV file. Stack: Next.js 14, Prisma, PostgreSQL.

**AI → Step 1 Response:**
> What needs to be built: export button in dashboard UI, server action to query and format data, CSV generation logic, file download trigger...
> Assumption: export includes all visible rows, not just current page...
> Risk: large datasets could time out...
> Question: should export run in background with email delivery, or direct download?

**You → Step 2:**
> Direct download is fine. Keep it simple — no background jobs. Max 5000 rows is acceptable.

**AI → Step 2 Response:**
> Implementation plan:
> Step 1: Add `exportDashboardData` server action in `server/actions/export.ts`...
> Step 2: Add `generateCsv` utility in `lib/csv.ts`...
> Step 3: Add Export button component...

**You → Step 3:**
> Implement Step 1 only.

---

## 🔄 Variations

**Micro-chain (small feature, 2 steps):**
```
STEP 1: Analyze this small feature. List what needs to change and any edge cases. Do not write code.
Feature: {description}

STEP 2 (after response): Implement it. Keep it minimal — no over-engineering.
```

**Architecture review chain:**
```
STEP 1: Review this architecture decision. List pros, cons, and alternatives. Do not recommend yet.
Decision: {description}

STEP 2: Based on my context ({constraints}), now give your recommendation with reasoning.
```

---

*Last updated: 2026-03-02*
