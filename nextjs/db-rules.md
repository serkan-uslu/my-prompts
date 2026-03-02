# Next.js Database Rules (Prisma + Supabase)

## 🎯 Purpose
Rules for database schema design, Prisma usage, and Supabase integration in Next.js projects. Prevents common ORM anti-patterns.

## 📋 Context
- Prisma ORM with PostgreSQL (or Supabase as PostgreSQL host)
- Migrations managed by Prisma
- Database access is server-only

## 💬 The Prompt / Rules

```
You are working on the database layer of a Next.js project using Prisma with PostgreSQL.
Follow these rules for all schema and query work.

**Schema Design**
- Use cuid() or uuid() for all IDs — never auto-increment integers for public-facing IDs
- Every model needs: id, createdAt, updatedAt
- Use enums for fields with a fixed set of values — not plain strings
- Add database indexes on all foreign keys and frequently filtered fields
- Use explicit relation names when a model has multiple relations to the same table
- Soft deletes: add deletedAt DateTime? and filter in queries — never hard delete user data

**Prisma Schema Conventions**
- Model names: PascalCase singular (User, BlogPost)
- Field names: camelCase (firstName, createdAt)
- Relation fields: singular for one-to-one/many-to-one, plural for one-to-many
- Always define the opposite side of every relation

**Query Functions**
Write all queries in server/db/{model}.ts:
- One file per model
- One function per operation — no multi-purpose query functions
- Always select only the fields you need — no select: undefined (SELECT *)
- Use findUniqueOrThrow or findFirstOrThrow when the record must exist
- Wrap multi-step operations in a transaction

**Query Patterns**
// Good — explicit select
const user = await prisma.user.findUnique({
  where: { id },
  select: { id: true, name: true, email: true }
})

// Bad — SELECT *
const user = await prisma.user.findUnique({ where: { id } })

**Pagination**
- Always paginate list queries — never return unlimited records
- Use cursor-based pagination for infinite scroll, offset for numbered pages
- Default page size: 20, maximum: 100

**Migrations**
- Never edit a migration file after it's been applied
- Migration names describe what changed: "add_email_verified_to_users"
- Run migrations in CI before deploying — never migrate in production manually
- Keep a seed.ts for development data

**Performance**
- Use prisma.$queryRaw only when Prisma's query builder can't express the query
- Add @db.VarChar(255) for email fields — don't use Text for known-length strings
- Profile slow queries with prisma.$on('query', ...) in development

**Supabase Specifics (if applicable)**
- Use Supabase for auth and storage, Prisma for all data queries
- Never mix Supabase client queries and Prisma queries for the same table
- Row Level Security: enable on all tables, write policies before writing queries
- Use the service role key only in Server Actions — never in client code
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{model_name}` | Prisma model name | `UserProfile` |
| `{db_host}` | Database host | `Supabase` / `Railway` |

## 📝 Example Output

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique @db.VarChar(255)
  name      String
  role      UserRole @default(USER)
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  deletedAt DateTime?

  @@index([email])
}

enum UserRole {
  USER
  ADMIN
}
```

## 🔄 Variations

- **Supabase only (no Prisma):** Replace query patterns with Supabase client patterns
- **Multi-tenant:** Add organizationId to every model, add composite indexes

---

*Last updated: 2026-03-02*
