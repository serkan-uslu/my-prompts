# Task Prompt — Commit Message

## 🎯 Purpose
Conventional commit message üretmek için. Git diff veya değişiklik açıklamasını verirsin, standart formatta commit message çıkar. Claude, GPT, Gemini hepsinde çalışır.

## 📋 Context
- Conventional Commits spec: `type(scope): description`
- 72 karakter sınırı (subject line)
- Body isteğe bağlı — karmaşık değişiklikler için

## 💬 The Prompt

```
Write a git commit message for the following changes.

Rules:
- Format: type(scope): short description
- Max 72 characters for the subject line
- Types: feat, fix, chore, refactor, docs, style, test, perf, ci
- Imperative mood: "add" not "added", "fix" not "fixed"
- No period at the end
- Lowercase everything except proper nouns
- scope is optional — use it when the change is clearly scoped to one area

If the change is complex (multiple concerns, non-obvious reason), add a body:
- One blank line after subject
- Explain WHY, not what (the diff shows what)
- Wrap at 72 characters

Output: subject line only, or subject + body if needed. No explanation, no options — just the commit message.

Changes:
{paste git diff or describe what changed}
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{changes}` | Git diff veya değişiklik açıklaması | `git diff` output veya "added dark mode toggle to header" |

## 📝 Example Output

```
feat(auth): add refresh token rotation on login

Previous implementation reused the same refresh token indefinitely.
Now generates a new refresh token on every successful login to limit
the window of exposure if a token is compromised.
```

```
fix(api): return 404 instead of 500 for missing user
```

```
chore: upgrade TanStack Query to v5
```

## 🔄 Variations

**Çoklu commit (büyük PR'ı böl):**
```
These changes cover multiple concerns. Suggest how to split them into separate commits, then write a commit message for each one.

Changes: {description}
```

**Emoji prefix isteyen projeler:**
```
Same rules, but prefix each commit with a single relevant emoji.
feat → ✨, fix → 🐛, chore → 🔧, docs → 📝, refactor → ♻️, perf → ⚡
```

---

*Last updated: 2026-03-02*
