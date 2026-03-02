# Task Prompt — Code Explanation

## 🎯 Purpose
Kodu farklı seviyelere göre açıklatmak için. Bir junior'a explain etmek, code review'a hazırlanmak, veya kendin anlamak istediğinde kullan.

## 📋 Context
- Seviyeye göre üç farklı varyant: junior, peer, rubber duck
- Claude, GPT, Gemini hepsinde çalışır

## 💬 The Prompt

### Variant A — Junior Developer Explanation
```
Explain the following code to a junior developer who knows basic JavaScript but hasn't seen this pattern before.

Rules:
- No unexplained jargon. If you must use a technical term, define it immediately.
- Use an analogy if it helps — but only if it genuinely clarifies, not just for the sake of it.
- Cover: what it does, why it's written this way (what problem this pattern solves), what would break if you removed or changed it.
- Keep it conversational, not lecture-style.

Code:
{paste code}
```

### Variant B — Peer Explanation (Code Review Prep)
```
Explain the following code as if you're briefing a peer before a code review.

Cover:
1. What this code does (2 sentences max)
2. The key design decisions made and why
3. What assumptions it makes
4. What could go wrong / edge cases it doesn't handle
5. What you'd want the reviewer to pay close attention to

Be direct. No padding.

Code:
{paste code}
```

### Variant C — Rubber Duck Debug
```
I don't understand why this code behaves the way it does. Walk me through it line by line.

For each meaningful block:
- What is this doing?
- What is the state/value at this point?
- Why might this produce unexpected behavior?

Don't fix it yet — just help me understand what's happening.

Code: {paste code}
Expected behavior: {what you expected}
Actual behavior: {what actually happens}
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{code}` | Açıklanacak kod | Herhangi bir kod bloğu |
| `{expected}` | Beklenen davranış | "Should return the filtered list" |
| `{actual}` | Gerçek davranış | "Returns an empty array" |

## 📝 Example Output (Variant A)

For this code:
```typescript
const user = users.find(u => u.id === targetId) ?? defaultUser
```

> "This line searches through the `users` array to find one specific user — the one whose `id` matches `targetId`. Think of it like flipping through a stack of ID cards until you find the right one.
>
> The `??` part is a safety net called the nullish coalescing operator. If `find()` comes back empty (no matching user exists), instead of crashing or returning `undefined`, it falls back to `defaultUser` — a safe placeholder value.
>
> If you removed the `?? defaultUser` part and no user was found, anything that tries to use `user.name` or `user.email` afterward would throw an error saying 'cannot read property of undefined'."

## 🔄 Variations

**Architecture explanation:**
```
Explain the architecture of this codebase section — not what each line does, but how the pieces fit together and why they're structured this way.

Code/files: {paste relevant code}
```

**Explain to non-technical stakeholder:**
```
Explain what this code does in plain English for someone non-technical.
No code terms. Focus on what the user experiences as a result of this code running.

Code: {paste code}
```

---

*Last updated: 2026-03-02*
