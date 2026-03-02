# Refactor Rules

## 🎯 Purpose
Rules for safe, focused refactoring with AI. Prevents the most common AI refactoring failure: changing behavior while changing structure.

## 📋 Context
- Refactoring existing working code
- Critical: behavior must not change
- TypeScript + React codebase

## 💬 The Prompt / Rules

```
You are refactoring the following code. Follow these rules strictly.

**The Prime Rule**
Do NOT change behavior. Only change structure.
If you find a bug while refactoring, do NOT fix it — report it and let me fix it separately.
If you're unsure whether a change affects behavior, leave it as-is and flag it.

**What You Can Do**
- Extract repeated logic into a named function
- Rename variables and functions to match naming conventions
- Simplify complex conditionals (only if logic is identical)
- Remove dead code (unused variables, unreachable branches)
- Move code to the right file/layer
- Split a large function into smaller named functions
- Replace manual implementations with built-in methods (filter vs for loop)

**What You Cannot Do**
- Change function signatures without asking
- Add new functionality
- Fix bugs (report them, don't fix them)
- Add error handling that wasn't there (behavior change)
- Change the order of operations unless guaranteed safe
- Swap a library for another library
- Add logging or debugging code

**Process**
1. Read all the code first before suggesting any changes
2. Identify what the code does (document it briefly)
3. Identify what's wrong with the structure (not behavior)
4. Make one type of change at a time — don't mix renaming with extraction
5. After each step, confirm the behavior is identical

**Output Format**
For each change:
- What you changed
- Why (structural reason)
- Risk level: Safe / Low Risk / Needs Review
- If Needs Review: why you're uncertain

Refactor the following code:
{code}
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{code}` | The code to refactor | Paste the code block |
| `{goal}` | What you want to achieve | `Extract API calls into a custom hook` |

## 📝 Example Output

**Identified behavior:** Fetches user data on mount, shows loading state, handles error.

**Changes made:**
1. Extracted fetch logic into `fetchUser()` — Safe
   - Before: async logic inline in useEffect
   - After: named function called from useEffect
   - Behavior: identical

2. Renamed `data` → `user` — Safe
   - Naming convention: variable name reflects its content

**Flags (do not fix automatically):**
- Line 34: Error is caught but not logged — may be intentional, check before adding logging

## 🔄 Variations

- **Extract Hook:** "Extract all state and data fetching logic from this component into a custom hook"
- **Split Component:** "Split this component into smaller components without changing what's rendered"
- **Clean Imports:** "Remove unused imports and organize them by: React, third-party, local"

---

*Last updated: 2026-03-02*
