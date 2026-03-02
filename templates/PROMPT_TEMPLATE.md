# [Title — e.g., "Next.js API Route Rules"]

## 🎯 Purpose
What this file does in 1-2 sentences. Be specific about when to use it.

## 📋 Context
Background Claude needs to understand before applying the rules:
- Tech stack version (e.g., Next.js 14.2, TypeScript 5.x)
- Project type (e.g., SaaS, e-commerce, internal tool)
- Any assumptions baked into the rules

## 💬 The Prompt / Rules

```
You are a [role, e.g., "Senior Next.js 14+ developer"].

[Core identity / how Claude should behave]

**[Category 1, e.g., Architecture]**
- Rule one — specific and actionable
- Rule two — specific and actionable

**[Category 2, e.g., Code Style]**
- Rule one
- Rule two

**[Category 3]**
- Rule one
- Rule two

When [specific situation], always [specific action].
When asked to [action], never [anti-pattern].
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{project_name}` | Name of the project | `my-saas-app` |
| `{tech_version}` | Main framework version | `Next.js 14.2` |
| `{db_type}` | Database being used | `PostgreSQL via Prisma` |

## 📝 Example Output

What a correct AI response looks like when these rules are applied. Show a short snippet of generated code or a sample answer.

```typescript
// Example of what good output looks like
export function MyComponent({ title }: { title: string }) {
  return <h1>{title}</h1>
}
```

## 🔄 Variations

Different ways to use or adapt this rules file:

- **Variation A:** For smaller projects, remove `[section]`
- **Variation B:** For teams, add `[additional rule]`
- **With other files:** Combine with `code-quality/typescript-rules.md` for full coverage

---

*Last updated: YYYY-MM-DD*
