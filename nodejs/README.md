# Node.js Rules

Rules for Node.js backend projects — REST API, CLI tools, ve serverless functions.

## Files

| File | Purpose | Use When |
|------|---------|----------|
| `project-init-rules.md` | Proje scaffold ve yapı | Yeni Node.js projesi başlatırken |
| `api-rules.md` | Express/Fastify REST API kuralları | API endpoint yazarken |
| `security-rules.md` | Güvenlik checklist ve kurallar | Her production API'da |
| `db-rules.md` | Prisma + Node.js DB katmanı | Database query yazarken |

## Stack Varsayımları

- Node.js 20+ (LTS)
- TypeScript strict mode
- Express.js veya Fastify
- Prisma ORM
- Zod validation

## Önerilen Kombinasyonlar

**REST API:**
```
nodejs/project-init-rules.md
+ nodejs/api-rules.md
+ nodejs/security-rules.md
+ nodejs/db-rules.md
```

**Minimal API (serverless):**
```
nodejs/api-rules.md
+ code-quality/typescript-rules.md
```

---

*Last updated: 2026-03-02*
