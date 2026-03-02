# TypeScript Rules

## 🎯 Purpose
TypeScript strict mode rules that keep the codebase type-safe without becoming bureaucratic. Eliminates the most common TypeScript anti-patterns.

## 📋 Context
- TypeScript 5.x with `strict: true`
- React + Next.js project
- No `any` policy (with documented exceptions)

## 💬 The Prompt / Rules

```
You are writing TypeScript in strict mode. Follow these rules for all TypeScript code.

**The No-any Rule**
- Never use `any` — it silently disables type checking
- When you'd reach for `any`, use: unknown (then narrow it), or a proper generic
- Exception: third-party library types that genuinely return any — add a comment explaining why
- `as` type assertions require a comment: // safe: we just validated this above

**Type vs Interface**
- Interface for object shapes that describe something (UserProfile, ApiResponse)
- Type for unions, intersections, mapped types, and utility types
- Don't mix — pick one convention per domain and stick to it
- Prefer interface for component props (allows declaration merging)

**Naming Conventions**
- Types/Interfaces: PascalCase — User, ApiResponse, CreateUserInput
- Generics: single uppercase letter for simple cases (T, K, V), descriptive for complex (TData, TError)
- No "I" prefix for interfaces (IUser) — this is not C#
- No "Type" suffix (UserType) — redundant

**Utility Types — Use Them**
- Omit<User, 'password'> — not a manually rewritten type
- Pick<User, 'id' | 'name'> — for projection types
- Partial<UpdateInput> — for optional update fields
- Required<Config> — when all fields must be provided
- Readonly<Config> — for immutable config objects

**Function Types**
- Always type function return values explicitly for public functions
- Use function keyword for named functions, arrow functions for callbacks
- Avoid Function type — always specify the signature

**Null and Undefined**
- Prefer undefined over null for optional values in TypeScript code
- null for database nullable fields (Prisma returns null)
- Use optional chaining (?.) and nullish coalescing (??) — avoid manual null checks

**Generics**
- Add type constraints when you know the shape: <T extends object>
- Default type params when there's a common case: <T = string>
- Don't over-generalize — if a function only works with User, type it as User

**Enums**
- Prefer const object + typeof over enum:
  const Role = { USER: 'USER', ADMIN: 'ADMIN' } as const
  type Role = typeof Role[keyof typeof Role]
- Reason: enums compile to JS objects with unexpected behavior; const objects don't

**Error Types**
- Don't catch errors as any — type them as unknown and narrow:
  catch (error) { if (error instanceof Error) { ... } }
- Create custom error classes for domain errors that need to be caught specifically

**Strict Mode Checklist**
When `noUncheckedIndexedAccess: true`:
- Array[index] returns T | undefined — always check before using
- When you know the index exists, use ! with a comment

Never disable strict rules with @ts-ignore — use @ts-expect-error with a comment if truly necessary.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{strict_level}` | Strictness required | `strict + noUncheckedIndexedAccess` |

## 📝 Example Output

```typescript
// Good — explicit types, no any
type UserRole = 'USER' | 'ADMIN'

interface User {
  id: string
  email: string
  role: UserRole
  name?: string
}

type CreateUserInput = Omit<User, 'id'>

async function createUser(input: CreateUserInput): Promise<User> {
  // ...
}
```

## 🔄 Variations

- **Library code:** Add stricter generics requirements, never use `object` type
- **API response types:** Use Zod schemas as the source of truth, infer TypeScript types from them

---

*Last updated: 2026-03-02*
