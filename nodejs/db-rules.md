# Node.js Database Rules (Prisma)

## 🎯 Purpose
Node.js + Prisma projelerinde database katmanı kuralları. Repository pattern, query optimizasyonu, ve migration yönetimi.

## 📋 Context
- Prisma ORM + PostgreSQL
- Node.js TypeScript backend
- nextjs/db-rules.md'yi tamamlar — Next.js'e özel olmayan kısımlar buraya taşındı

## 💬 The Prompt / Rules

```
You are building the database layer of a Node.js application using Prisma.
Follow these rules for all database work. See also nextjs/db-rules.md for schema conventions.

**Repository Pattern**
Her model için bir repository file: src/repositories/{model}.repository.ts
Controller ve Service doğrudan Prisma'yı çağırmaz — sadece repository üzerinden.

// repositories/users.repository.ts
export const UsersRepository = {
  findById: (id: string) =>
    prisma.user.findUnique({ where: { id }, select: safeUserSelect }),

  findByEmail: (email: string) =>
    prisma.user.findUnique({ where: { email }, select: safeUserSelect }),

  create: (data: CreateUserData) =>
    prisma.user.create({ data, select: safeUserSelect }),

  update: (id: string, data: UpdateUserData) =>
    prisma.user.update({ where: { id }, data, select: safeUserSelect }),

  delete: (id: string) =>
    prisma.user.delete({ where: { id } }),
}

// select tanımı — response'dan hassas alanlar çıkarılmış
const safeUserSelect = {
  id: true,
  email: true,
  name: true,
  role: true,
  createdAt: true,
  // password: false — asla dönme
} satisfies Prisma.UserSelect

**Prisma Client Singleton**
Tek bir Prisma Client instance — bağlantı havuzu için kritik.
config/database.ts dosyasından import et.
Her dosyada new PrismaClient() yapma.

**Transaction Kuralları**
Birden fazla DB operasyonu atomik olmalıysa transaction:

// Interactive transaction
const result = await prisma.$transaction(async (tx) => {
  const user = await tx.user.create({ data: userData })
  const profile = await tx.profile.create({ data: { ...profileData, userId: user.id } })
  return { user, profile }
})

// Batch transaction (bağımsız operasyonlar)
await prisma.$transaction([
  prisma.user.update({ where: { id }, data: { status: 'ACTIVE' } }),
  prisma.log.create({ data: { action: 'USER_ACTIVATED', userId: id } }),
])

Transaction timeout varsayılan 5 saniye — uzun işlemler için artır:
prisma.$transaction(async (tx) => { ... }, { timeout: 30000 })

**Pagination — Cursor-based (Tercih)**
// Cursor-based — büyük veri setleri, infinite scroll için
async function getPostsCursor(cursor?: string, limit = 20) {
  return prisma.post.findMany({
    take: limit + 1,  // bir fazla çek, sonraki sayfa var mı anlamak için
    ...(cursor && { skip: 1, cursor: { id: cursor } }),
    orderBy: { createdAt: 'desc' },
  })
}

// Offset-based — numbered pagination için (sayfa 1, 2, 3...)
async function getPostsOffset(page = 1, limit = 20) {
  const [posts, total] = await prisma.$transaction([
    prisma.post.findMany({ skip: (page - 1) * limit, take: limit }),
    prisma.post.count(),
  ])
  return { posts, total, totalPages: Math.ceil(total / limit) }
}

**N+1 Query Problemi**
// Yanlış — her post için ayrı author query (N+1)
const posts = await prisma.post.findMany()
for (const post of posts) {
  const author = await prisma.user.findUnique({ where: { id: post.authorId } })
}

// Doğru — tek query ile include
const posts = await prisma.post.findMany({
  include: { author: { select: { id: true, name: true } } }
})

**Soft Delete Pattern**
Kullanıcı datasını hard delete etme:
1. Model'e deletedAt DateTime? alanı ekle
2. Her query'e where: { deletedAt: null } ekle
3. Prisma middleware ile otomatikleştir:

prisma.$use(async (params, next) => {
  if (params.model === 'User') {
    if (params.action === 'delete') {
      params.action = 'update'
      params.args.data = { deletedAt: new Date() }
    }
    if (['findMany', 'findFirst', 'count'].includes(params.action)) {
      params.args.where = { ...params.args.where, deletedAt: null }
    }
  }
  return next(params)
})

**Migration Workflow**
Development:
  npx prisma migrate dev --name {change_description}

Production:
  npx prisma migrate deploy   // sadece mevcut migration'ları uygular, yeni oluşturmaz

Asla production'da migrate dev çalıştırma.
Migration dosyaları git'e commit'le — source of truth bu dosyalar.

**Seeding**
src/database/seed.ts — idempotent olmalı (tekrar çalıştırılabilir)
upsert kullan, create değil:
  prisma.user.upsert({ where: { email: '...' }, update: {}, create: { ... } })
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{model_name}` | Prisma model adı | `User`, `Order` |
| `{change_description}` | Migration açıklaması | `add_refresh_tokens_table` |

## 📝 Example Output

```typescript
// repositories/orders.repository.ts
import { prisma } from '@/config/database'
import type { Prisma } from '@prisma/client'

const orderSelect = {
  id: true,
  status: true,
  total: true,
  createdAt: true,
  user: { select: { id: true, name: true, email: true } },
  items: { select: { id: true, quantity: true, product: { select: { id: true, name: true } } } },
} satisfies Prisma.OrderSelect

export const OrdersRepository = {
  findById: (id: string) =>
    prisma.order.findUnique({ where: { id }, select: orderSelect }),

  findByUser: (userId: string, page = 1, limit = 20) =>
    prisma.$transaction([
      prisma.order.findMany({
        where: { userId, deletedAt: null },
        select: orderSelect,
        orderBy: { createdAt: 'desc' },
        skip: (page - 1) * limit,
        take: limit,
      }),
      prisma.order.count({ where: { userId, deletedAt: null } }),
    ]),
}
```

## 🔄 Variations

- **MongoDB:** Prisma + MongoDB adapter — findMany yerine aggregate bazı durumlarda
- **Raw SQL:** Karmaşık raporlama sorgularında `prisma.$queryRaw` + tagged template literals

---

*Last updated: 2026-03-02*
