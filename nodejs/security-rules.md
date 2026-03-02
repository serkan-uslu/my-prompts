# Node.js Security Rules

## 🎯 Purpose
Production Node.js API'ları için güvenlik zorunlulukları. Her endpoint açılmadan önce bu listeyi kontrol et. Bir güvenlik açığı tüm projeyi bozabilir.

## 📋 Context
- Express.js + TypeScript
- Public veya authenticated REST API
- JWT veya session tabanlı auth

## 💬 The Prompt / Rules

```
You are reviewing or building a Node.js API. Apply these security rules.
These are non-negotiable — do not skip or simplify them.

**HTTP Security Headers — helmet() Zorunlu**
import helmet from 'helmet'
app.use(helmet())   // tek satır, 11 farklı güvenlik header'ı ayarlar

Kontrol et:
- Content-Security-Policy ✓
- X-Frame-Options ✓
- X-Content-Type-Options ✓
- Strict-Transport-Security (HTTPS'de) ✓

**CORS — Açık * Asla**
import cors from 'cors'

app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') ?? [],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
}))

// Yanlış: app.use(cors()) — tüm origin'lere açık

**Rate Limiting — Her Public Endpoint'e**
import rateLimit from 'express-rate-limit'

// Global limit
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 dakika
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
})

// Auth endpoint'leri daha sıkı
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 10,
  message: { error: { code: 'RATE_LIMITED', message: 'Too many attempts' } },
})

app.use(globalLimiter)
app.use('/api/auth', authLimiter)

**Authentication**
JWT kuralları:
- Secret en az 32 karakter, environment variable'dan oku
- Token expiry: access token 15dk, refresh token 7 gün
- Refresh token rotation: her kullanımda yeni token
- Token'ı Authorization: Bearer {token} header'ında al — query string asla

Middleware:
export function authenticate(req: Request, res: Response, next: NextFunction) {
  const token = req.headers.authorization?.split(' ')[1]
  if (!token) return res.status(401).json({ error: { code: 'UNAUTHORIZED' } })

  try {
    req.user = jwt.verify(token, env.JWT_SECRET) as JWTPayload
    next()
  } catch {
    res.status(401).json({ error: { code: 'INVALID_TOKEN' } })
  }
}

**Authorization — Her Endpoint'de Kontrol Et**
Authentication (kim olduğunu bilmek) yeterli değil.
Authorization (ne yapabileceğini bilmek) de gerekli.

// Yanlış: sadece authenticate() — kullanıcı başkasının kaydını görebilir
router.get('/:id', authenticate, UsersController.show)

// Doğru: authenticate + authorize
router.get('/:id', authenticate, authorizeOwnerOrAdmin, UsersController.show)

function authorizeOwnerOrAdmin(req, res, next) {
  if (req.user.id !== req.params.id && req.user.role !== 'ADMIN') {
    return res.status(403).json({ error: { code: 'FORBIDDEN' } })
  }
  next()
}

**Input Validation — Zod ile Her Şey**
Asla trust etme:
- req.body — her zaman validate et
- req.params — ID format kontrol et (cuid, uuid, integer?)
- req.query — coerce et ve validate et
- req.headers — custom header'ları kontrol et

// ID validation
const ParamSchema = z.object({
  id: z.string().cuid(),
})

**SQL Injection**
Prisma ORM kullanıyorsan parametreli queries otomatik.
Prisma.$queryRaw kullanıyorsan:
// Yanlış:
prisma.$queryRaw`SELECT * FROM users WHERE email = '${email}'`  // injection!

// Doğru:
prisma.$queryRaw`SELECT * FROM users WHERE email = ${email}`    // tagged template

**Sensitive Data**
- Password hash'lenmiş sakla: bcrypt, argon2 (argon2 tercih et)
- Response'da şifre, token, secret dönme — Prisma select ile aç
- Log'a şifre, kredi kartı, kişisel veri yazma
- Error message'ında stack trace döndürme (production)
- .env'i commit'leme

**Dependency Güvenliği**
- npm audit komutu ile düzenli kontrol et
- Dependabot veya Snyk aktif et
- Lock file'ı (package-lock.json) commit'le
- Kullanılmayan dependency'leri kaldır

**Production Checklist**
☐ NODE_ENV=production set edildi mi?
☐ Tüm env variable'lar Zod ile validate ediliyor mu?
☐ HTTPS zorunlu mu?
☐ Rate limiting aktif mi?
☐ CORS origin list'i production URL'leri mi?
☐ Error response'ları stack trace içermiyor mu?
☐ Bağımlılıklar güncel mi? (npm audit clean)
☐ JWT secret production'da güçlü ve benzersiz mi?
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{allowed_origins}` | İzin verilen domain'ler | `https://myapp.com,https://admin.myapp.com` |
| `{token_expiry}` | JWT geçerlilik süresi | `15m` (access), `7d` (refresh) |

## 📝 Example Output

```typescript
// middleware/auth.ts
import jwt from 'jsonwebtoken'
import { env } from '@/config/env'

interface JWTPayload {
  sub: string  // user ID
  role: string
  iat: number
  exp: number
}

declare global {
  namespace Express {
    interface Request {
      user: JWTPayload
    }
  }
}

export function authenticate(req: Request, res: Response, next: NextFunction) {
  const authHeader = req.headers.authorization
  if (!authHeader?.startsWith('Bearer ')) {
    return res.status(401).json({ error: { code: 'UNAUTHORIZED', message: 'No token provided' } })
  }

  const token = authHeader.slice(7)
  try {
    req.user = jwt.verify(token, env.JWT_SECRET) as JWTPayload
    next()
  } catch {
    res.status(401).json({ error: { code: 'INVALID_TOKEN', message: 'Token expired or invalid' } })
  }
}
```

## 🔄 Variations

- **Session-based Auth:** JWT yerine express-session + Redis store
- **API Key Auth:** API key validation middleware (machine-to-machine)
- **OAuth:** Passport.js + OAuth2 stratejileri

---

*Last updated: 2026-03-02*
