# Code Review Rules

## 🎯 Purpose
A structured prompt for asking Claude to review code. Gets specific, actionable feedback organized by priority — not a wall of style comments.

## 📋 Context
- Works on any code — paste the code after the prompt
- Useful before submitting a PR or merging a feature
- Focuses on bugs and correctness first, style second

## 💬 The Prompt / Rules

```
Review the following code. Organize feedback in this exact structure:

**🔴 Critical (Must Fix)**
Bugs, security issues, data loss risks, or incorrect behavior.
For each issue: what is wrong, why it matters, how to fix it.

**🟡 Important (Should Fix)**
Performance issues, missing error handling, logic that will break under edge cases,
TypeScript type safety gaps.

**🟢 Suggestions (Nice to Have)**
Naming improvements, readability, simplification, better patterns.

**✅ What's Good**
Note what's done well — this is equally important for learning.

Rules for the review:
- Be specific — reference line numbers or code snippets
- Don't flag style issues as critical
- Don't suggest changes that make the code more complex without clear benefit
- If a pattern is repeated and wrong, say it once — don't list every occurrence
- If something is unclear to you, say "I'm not sure if this is intentional: ..." rather than assuming it's a bug

Stack context: {stack_context}
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{stack_context}` | Tech stack of the code | `Next.js 14 Server Action, TypeScript strict` |

## 📝 Example Output

**🔴 Critical**
- Line 23: `user.id` is used without checking if `user` exists. If `getUser()` returns null (which it can per the type), this throws. Fix: add a null check before using user.

**🟡 Important**
- No error handling in the Server Action. If the DB call fails, the error propagates unhandled to the client. Wrap in try/catch and return `{ success: false, error: ... }`.

**🟢 Suggestions**
- `userData` → `user` — the "Data" suffix is redundant when the variable clearly holds a user object.

**✅ What's Good**
- Zod validation is applied before any DB call — correct order.
- Loading and error states are handled in the UI.

## 🔄 Variations

- **Security Review:** Add "Check for: SQL injection, XSS, CSRF, auth bypass, exposed secrets"
- **Performance Review:** Add "Check for: N+1 queries, unnecessary re-renders, large bundle imports"
- **PR Review:** Use with `git diff` output pasted in

---

*Last updated: 2026-03-02*
