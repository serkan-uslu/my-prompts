# Next.js API Route Rules

## 🎯 Purpose
Conventions for Next.js App Router API routes (`app/api/`). Use when building webhook endpoints, third-party integrations, or public APIs.

## 📋 Context
- Next.js 14+ App Router route handlers (`route.ts`)
- Prefer Server Actions for internal mutations — use API routes only for external endpoints
- TypeScript strict mode

## 💬 The Prompt / Rules

```
You are writing Next.js App Router API route handlers. Follow these rules strictly.

**When to Use API Routes vs Server Actions**
Use API routes for:
- Webhook endpoints (Stripe, GitHub, etc.)
- Endpoints consumed by external services or mobile apps
- File upload/download endpoints
- When you need explicit HTTP status codes

Use Server Actions for:
- Form submissions from your own UI
- Mutations triggered by your own client components

**File Structure**
app/api/{resource}/route.ts         — collection: GET (list), POST (create)
app/api/{resource}/[id]/route.ts    — item: GET, PUT/PATCH, DELETE

**Route Handler Pattern**
export async function GET(request: Request) {
  try {
    // 1. Auth check
    // 2. Parse and validate params/body
    // 3. Business logic
    // 4. Return response
  } catch (error) {
    return Response.json({ error: 'Internal server error' }, { status: 500 })
  }
}

**Authentication**
- Check authentication at the top of every handler — before any other logic
- Return 401 (not 403) when not authenticated
- Return 403 when authenticated but not authorized
- Never trust client-provided user IDs — get the user from the session

**Input Validation**
- Validate all input with Zod before using it
- Return 400 with field-level errors for validation failures
- For query params: use URL.searchParams, validate with Zod

**Response Format**
Always return JSON with consistent shape:
Success: { data: T }
Error:   { error: string, details?: ZodIssue[] }

Use correct HTTP status codes:
200 — GET success, PUT/PATCH success
201 — POST success (created)
400 — Invalid input
401 — Not authenticated
403 — Not authorized
404 — Resource not found
409 — Conflict (duplicate)
500 — Server error

**Webhook Endpoints**
- Verify the webhook signature before processing — reject invalid signatures with 400
- Return 200 immediately, process async in the background
- Idempotency: check if the event was already processed
- Log webhook payload for debugging

**Rate Limiting**
- Apply rate limiting to all public endpoints
- Use Upstash Rate Limit or similar for serverless-compatible limiting
- Return 429 with Retry-After header when rate limited

**No Sensitive Data in Responses**
- Never return passwords, tokens, or internal IDs you don't want exposed
- Explicitly select/omit fields in response objects — don't spread Prisma objects
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{resource}` | API resource name | `users`, `posts`, `webhooks` |
| `{auth_provider}` | Auth solution | `NextAuth.js`, `Clerk` |

## 📝 Example Output

```typescript
// app/api/posts/route.ts
export async function GET(request: Request) {
  const session = await getServerSession()
  if (!session) return Response.json({ error: 'Unauthorized' }, { status: 401 })

  const { searchParams } = new URL(request.url)
  const page = parseInt(searchParams.get('page') ?? '1')

  const posts = await getPosts({ userId: session.user.id, page })
  return Response.json({ data: posts })
}
```

## 🔄 Variations

- **Public API:** Add API key validation middleware
- **Stripe Webhooks:** Add `stripe.webhooks.constructEvent()` signature check

---

*Last updated: 2026-03-02*
