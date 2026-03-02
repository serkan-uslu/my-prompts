# Node.js REST API Rules

## 🎯 Purpose
Express.js ile REST API yazma kuralları — route organizasyonu, controller/service ayrımı, validation, ve tutarlı response formatı.

## 📋 Context
- Express.js + TypeScript
- Zod ile input validation
- Prisma ile database
- JWT auth (veya session-based)

## 💬 The Prompt / Rules

```
You are building a REST API with Express.js and TypeScript.
Follow these rules for all API development.

**Katmanlı Mimari — Her Katmanın Sorumluluğu**
Route     → URL + HTTP method tanımlama, middleware bağlama
Controller → Request parse, validation call, service call, response format
Service    → Business logic, orchestration — DB'yi doğrudan çağırmaz
Repository → DB queries (Prisma calls) — tek veri erişim noktası

Hiçbir zaman:
- Controller içinde Prisma çağrısı yok
- Service içinde req/res objesi yok
- Route içinde business logic yok

**Route Tanımı**
// routes/users.routes.ts
import { Router } from 'express'
import { authenticate } from '@/middleware/auth'
import * as UsersController from '@/controllers/users.controller'

const router = Router()

router.get('/', authenticate, UsersController.list)
router.get('/:id', authenticate, UsersController.show)
router.post('/', authenticate, UsersController.create)
router.patch('/:id', authenticate, UsersController.update)
router.delete('/:id', authenticate, UsersController.remove)

export default router

**Controller Pattern**
// controllers/users.controller.ts
export async function create(req: Request, res: Response, next: NextFunction) {
  try {
    const body = CreateUserSchema.parse(req.body)  // validate first
    const user = await UsersService.create(body)
    res.status(201).json({ data: user })
  } catch (error) {
    next(error)  // errorHandler middleware'e gönder
  }
}

**Response Format — Her Zaman Aynı Shape**
Başarı:
{ "data": T }                        — tek kayıt
{ "data": T[], "meta": { total, page, limit } }  — liste

Hata:
{ "error": { "code": string, "message": string, "details"?: object } }

HTTP Status Codes:
200 — GET başarı, PATCH/PUT başarı
201 — POST başarı (created)
204 — DELETE başarı (no content)
400 — Validation hatası (Zod error)
401 — Authentication yok
403 — Authorization yok (authenticated ama yetkisiz)
404 — Kayıt bulunamadı
409 — Conflict (unique constraint, duplicate)
422 — Business rule ihlali
429 — Rate limit
500 — Server error

**Validation Middleware**
// middleware/validate.ts
import { ZodSchema, ZodError } from 'zod'

export function validate(schema: ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      req.body = schema.parse(req.body)
      next()
    } catch (error) {
      if (error instanceof ZodError) {
        return res.status(400).json({
          error: {
            code: 'VALIDATION_ERROR',
            message: 'Invalid input',
            details: error.flatten().fieldErrors,
          },
        })
      }
      next(error)
    }
  }
}

// Route'da kullanım:
router.post('/', validate(CreateUserSchema), UsersController.create)

**Error Handler Middleware**
// middleware/errorHandler.ts — app.ts'de en son register edilir
export function errorHandler(err: Error, req: Request, res: Response, next: NextFunction) {
  // Zod hataları validate middleware'de yakalanır — buraya gelmez

  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      error: { code: err.code, message: err.message }
    })
  }

  // Prisma unique constraint
  if (err.code === 'P2002') {
    return res.status(409).json({
      error: { code: 'CONFLICT', message: 'Already exists' }
    })
  }

  // Beklenmedik hatalar
  logger.error(err)
  res.status(500).json({
    error: { code: 'INTERNAL_ERROR', message: 'Something went wrong' }
  })
}

**Pagination Pattern**
Her liste endpoint'i pagination kabul eder:
GET /users?page=1&limit=20&sort=createdAt&order=desc

Validation:
const PaginationSchema = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
})

Response'da meta:
{ data: users, meta: { total: 150, page: 1, limit: 20, totalPages: 8 } }

**Async Handler Wrapper**
Express async hataları yakalamazın — wrapper kullan:
function asyncHandler(fn: RequestHandler): RequestHandler {
  return (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next)
}

// Kullanım:
router.get('/:id', asyncHandler(UsersController.show))

**Logging**
- Her request: method, path, status, response time, user ID
- Her hata: full stack trace, request body (sensitive field'lar olmadan)
- Pino kullan — JSON format, production'da parse edilebilir
- Log level: development'ta debug, production'ta info
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{resource}` | API resource | `users`, `products`, `orders` |
| `{auth_method}` | Auth tipi | `JWT Bearer token` |

## 📝 Example Output

```typescript
// services/users.service.ts
import { UsersRepository } from '@/repositories/users.repository'
import type { CreateUserInput } from '@/types'

export class UsersService {
  static async create(input: CreateUserInput) {
    const existing = await UsersRepository.findByEmail(input.email)
    if (existing) throw new AppError('CONFLICT', 'Email already in use', 409)

    const hashed = await hashPassword(input.password)
    return UsersRepository.create({ ...input, password: hashed })
  }
}
```

## 🔄 Variations

- **Fastify:** Plugin sistemi ve schema-based validation (JSON Schema) kullan
- **GraphQL:** Apollo Server veya Pothos ile — REST yerine type-first schema

---

*Last updated: 2026-03-02*
