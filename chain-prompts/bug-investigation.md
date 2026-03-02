# Chain Prompt — Bug Investigation

## 🎯 Purpose
Zor bug'ları sistematik olarak çözmek için. AI'ın direkt "şunu dene, şunu dene" listesi vermesini engeller — önce hipotez, sonra fix, sonra risk değerlendirmesi.

## 📋 Context
- 3 adımlı chain: Hipotez → Fix → Risk
- debug-rules.md ile birlikte kullanılabilir (context sağlamak için)
- Claude, GPT, Gemini hepsinde çalışır

## 💬 The Chain

### STEP 1 — Hypothesis (Send First)
```
I have a bug. Before suggesting any fix, tell me your hypothesis about the root cause.

Rules:
- Give ONE most likely hypothesis, not a list of ten possibilities
- Explain WHY this would produce the observed behavior
- Tell me what one thing I can check to confirm or disprove your hypothesis
- If you don't have enough information, tell me exactly what you need — don't guess

Bug context:
- Expected: {what should happen}
- Actual: {what actually happens}
- Error (if any): {exact error message}
- When it happens: {steps to reproduce}

Relevant code:
{paste code}

Stop here. Wait for me to confirm or add information.
```

### STEP 2 — The Fix (Send After Confirming Hypothesis)
```
{Your confirmation: "Your hypothesis is correct" or "Not quite — here's what I found: {finding}"}

Now suggest the fix. Rules:
- Minimal change — fix only what's broken, don't refactor adjacent code
- Show me the exact diff: what to remove, what to add
- If there are two valid approaches, show both and tell me the tradeoff in one sentence each
- Do not apply the fix yet — show it to me first
```

### STEP 3 — Risk Check (Send After Approving Fix)
```
I'll apply this fix. Before I do:
1. What is the regression risk? What else could break?
2. Is there a test I should write or run to verify this fix works?
3. Anything I should watch for in production after deploying?

Then I'll confirm and we're done.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{expected}` | Beklenen davranış | "Form submits and redirects to /dashboard" |
| `{actual}` | Gerçek davranış | "Stays on the form page, no error shown" |
| `{error}` | Hata mesajı | `NEXT_REDIRECT` caught in try/catch |

## 📝 Example Flow

**You → Step 1:**
> Expected: Server Action redirects to /dashboard after form submit.
> Actual: Page stays on the form. No visible error.
> Error: NEXT_REDIRECT appears in server logs.
> Code: [paste Server Action with try/catch wrapping redirect()]

**AI → Step 1 Response:**
> Hypothesis: `redirect()` in Next.js works by throwing a special error internally — `NEXT_REDIRECT`. Your try/catch block is catching this error before it can execute the redirect.
>
> To confirm: add `console.log(error)` in your catch block. You'll see the `NEXT_REDIRECT` error being caught there.

**You → Step 2:**
> Confirmed. The catch is eating the redirect.

**AI → Step 2 Response:**
> Fix: re-throw redirect errors in the catch block.
>
> Remove:
> ```typescript
> } catch (error) {
>   console.error(error)
> }
> ```
> Add:
> ```typescript
> } catch (error) {
>   if (isRedirectError(error)) throw error
>   console.error(error)
> }
> ```
> Import: `import { isRedirectError } from 'next/dist/client/components/redirect'`

**You → Step 3:**
> Looks right. Risk check?

**AI → Step 3 Response:**
> Regression risk: low. Only affects error handling in this action.
> Test: submit the form with valid data — confirm redirect happens. Submit with invalid data — confirm error is shown, no redirect.
> Production: nothing specific to watch. This is a silent fix.

---

## 🔄 Variations

**Performance bug:**
```
STEP 1: This operation is slow. Hypothesize why without looking at the code — just based on the description. What are the most common causes of this type of slowness?
Context: {describe the slow operation}

STEP 2: Here's the code. Confirm or revise your hypothesis.
Code: {paste code}

STEP 3: Suggest the optimization. Estimate the expected improvement.
```

**Intermittent bug (hard to reproduce):**
```
This bug happens rarely and I can't reproduce it reliably.
Known facts: {list what you know}
Code path: {paste the relevant code}

Don't give a fix yet. Tell me: what logging would you add to capture the state when this bug occurs?
```

---

*Last updated: 2026-03-02*
