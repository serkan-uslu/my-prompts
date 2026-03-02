# Debug Rules

## 🎯 Purpose
A structured context template for debugging with Claude. Getting Claude to help debug without this template usually produces generic advice. With it, you get targeted diagnosis.

## 📋 Context
- Use when stuck on a bug for more than 15 minutes
- Paste this template, fill it in, then paste the relevant code
- Works for any language or framework

## 💬 The Prompt / Rules

```
I'm debugging an issue. Here is all the context:

**Environment**
- Framework/Language: {framework}
- Version: {version}
- OS: {os}

**What I expected to happen:**
{expected_behavior}

**What actually happened:**
{actual_behavior}

**Error message (exact):**
```
{error_message}
```

**Steps to reproduce:**
1. {step_1}
2. {step_2}
3. {step_3}

**What I've already tried:**
- {tried_1}
- {tried_2}

**Relevant code:**
```{language}
{code}
```

**Relevant data (if applicable):**
```json
{sample_data}
```

Now help me debug this. Please:
1. Identify the most likely cause based on the evidence
2. Explain why this would produce the observed behavior
3. Give me one specific thing to check or change first (not a list of 10 things)
4. If you're not sure, say what information would help you narrow it down
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{framework}` | Tech stack | `Next.js 14, TypeScript` |
| `{expected_behavior}` | What should happen | `Form submits and redirects to /dashboard` |
| `{actual_behavior}` | What does happen | `Form submits but stays on the same page` |
| `{error_message}` | Exact error text | `Error: NEXT_REDIRECT` |

## 📝 Example Output

Claude's ideal response to this template:

> "The most likely cause is that `NEXT_REDIRECT` is being caught by your try/catch block in the Server Action. In Next.js, `redirect()` works by throwing a special error — if you wrap your Server Action in try/catch, you catch the redirect before it can execute.
>
> **Fix first:** Move `redirect()` outside your try/catch block, or re-throw if the error is a redirect:
> ```typescript
> catch (error) {
>   if (isRedirectError(error)) throw error // re-throw redirects
>   // handle actual errors
> }
> ```"

## 🔄 Variations

- **Quick Debug:** Use just the "expected / actual / error" section when the bug is simple
- **Network Debug:** Add "Request payload:" and "Response:" sections
- **Performance Debug:** Replace error message with "Slow operation: X takes Nms, expected <Yms"

---

*Last updated: 2026-03-02*
