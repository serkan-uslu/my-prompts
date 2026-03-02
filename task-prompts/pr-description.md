# Task Prompt — PR Description

## 🎯 Purpose
GitHub Pull Request açıklaması yazmak için. Değişiklikleri verirsin, reviewer'ın hızlıca anlayabileceği bir PR description üretir. Claude, GPT, Gemini hepsinde çalışır.

## 📋 Context
- GitHub PR markdown formatı
- Reviewer'ın bağlamı yok — her şeyi açıkla
- Test planı özellikle önemli — reviewer'a ne bakacağını söyle

## 💬 The Prompt

```
Write a GitHub Pull Request description for the following changes.

Structure (use these exact headings):

## What
One or two sentences. What does this PR do? Lead with the outcome, not the implementation.

## Why
One or two sentences. Why was this change needed? What problem does it solve?

## Changes
Bullet list of the meaningful changes. Skip obvious refactors and minor fixes unless they affect behavior. Group by area if there are many changes.

## How to Test
Numbered checklist. Specific steps a reviewer can follow to verify this works.
Include: what to look for, what the before state was, what the after state should be.

## Notes (optional)
Only include if there's something non-obvious: a tradeoff made, a known issue deferred, a dependency on another PR.

Rules:
- No filler phrases ("This PR aims to...", "In this PR we...")
- Present tense: "adds", "fixes", "removes"
- If a section has nothing meaningful to say, omit it
- Keep the whole thing under 300 words unless the PR is genuinely complex

Changes:
{paste diff, commit list, or describe what changed}
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{changes}` | PR değişiklikleri | `git log main..HEAD --oneline` veya açıklama |

## 📝 Example Output

```markdown
## What
Adds refresh token rotation to the authentication flow. Tokens are now invalidated after a single use.

## Why
The previous implementation reused refresh tokens indefinitely. A leaked token could be used to maintain persistent access. This limits the exposure window to the token's TTL.

## Changes
- `server/auth/refresh.ts` — generates a new refresh token on every call, invalidates the old one
- `middleware/auth.ts` — updated token validation to check against the stored token hash
- `db/schema.prisma` — added `refreshTokenHash` field to `Session` model

## How to Test
1. Log in and capture the refresh token from the cookie
2. Use the token to call `/api/auth/refresh` — should return a new access token
3. Try using the same refresh token again — should return 401
4. Verify the old token no longer works after rotation

## Notes
Existing sessions will be invalidated on deploy. Users will need to log in again once.
```

## 🔄 Variations

**Küçük / tek dosya değişikliği:**
```
This is a small change. Write a concise PR description — What and How to Test only. No other sections needed.

Change: {description}
```

**Breaking change içeriyorsa:**
```
Same format, but add a ## Breaking Changes section at the top with a ⚠️ warning.
Describe what breaks and what the migration path is.
```

---

*Last updated: 2026-03-02*
