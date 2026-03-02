# Node.js Project Initialization Rules

## 🎯 Purpose
Yeni bir Node.js + TypeScript backend projesini production-ready bir yapıyla başlatmak. Express veya Fastify, Prisma, Zod ile clean architecture.

## 📋 Context
- Node.js 20+ (LTS)
- TypeScript strict mode
- Express.js (veya Fastify)
- Prisma ORM
- Zod validation

## 💬 The Prompt / Rules

```
You are setting up a new Node.js + TypeScript backend project called {project_name}.
Follow these initialization rules strictly. Do not add any feature code — only the scaffold.

**Project Init**
mkdir {project_name} && cd {project_name}
npm init -y
npm install -D typescript ts-node-dev @types/node tsx

**tsconfig.json**
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "paths": { "@/*": ["./src/*"] }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}

**Required Dependencies**
Web framework:
  npm install express
  npm install -D @types/express

Validation:
  npm install zod

Database:
  npm install @prisma/client
  npm install -D prisma
  npx prisma init

Security middleware:
  npm install helmet cors express-rate-limit

Logging:
  npm install pino pino-pretty

Environment:
  npm install dotenv

**Directory Structure**
src/
  index.ts          — server başlangıç noktası
  app.ts            — Express app (middleware, routes)
  config/
    env.ts          — validated environment variables
    database.ts     — Prisma client singleton
  routes/
    index.ts        — tüm route'ları bir araya getirir
    {resource}.routes.ts
  controllers/
    {resource}.controller.ts
  services/
    {resource}.service.ts
  middleware/
    auth.ts
    errorHandler.ts
    validate.ts     — Zod validation middleware
  types/
    express.d.ts    — Express Request augmentation
    index.ts

**config/env.ts — Type-safe Environment Variables**
import { z } from 'zod'

const EnvSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
})

export const env = EnvSchema.parse(process.env)

// Eğer parse başarısız olursa uygulama başlamaz — missing env var erken yakalanır

**config/database.ts — Prisma Singleton**
import { PrismaClient } from '@prisma/client'

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient }

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({ log: ['query', 'error', 'warn'] })

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma

**app.ts — Middleware Sırası**
Sıra önemlidir:
1. helmet() — security headers
2. cors() — CORS policy
3. express.json() — body parser
4. express.urlencoded() — form data
5. rateLimit() — rate limiting
6. routes — business logic
7. 404 handler
8. error handler (en son)

**npm Scripts**
"scripts": {
  "dev": "tsx watch src/index.ts",
  "build": "tsc --project tsconfig.json",
  "start": "node dist/index.js",
  "db:migrate": "prisma migrate dev",
  "db:studio": "prisma studio",
  "db:seed": "tsx src/database/seed.ts"
}

**.env**
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/{project_name}
JWT_SECRET=

.env.example — aynı key'ler, boş değerler, commit'le.
.env — asla commit'leme.

**Git**
- .gitignore: node_modules/, dist/, .env, *.log
- İlk commit: "chore: initial Node.js + TypeScript project setup"
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{project_name}` | Proje adı | `my-api` |
| `{web_framework}` | Web framework | `Express.js` / `Fastify` |

## 📝 Example Output

```
my-api/
├── src/
│   ├── index.ts        ← server start
│   ├── app.ts          ← Express setup
│   ├── config/
│   │   ├── env.ts      ← Zod-validated env
│   │   └── database.ts ← Prisma singleton
│   ├── routes/
│   ├── controllers/
│   ├── services/
│   └── middleware/
│       ├── errorHandler.ts
│       └── validate.ts
├── prisma/
│   └── schema.prisma
├── .env.example
└── tsconfig.json
```

## 🔄 Variations

- **Fastify:** `npm install fastify @fastify/cors @fastify/helmet` — middleware sistemi farklı
- **Serverless:** src/ yerine functions/ klasörü, her function bağımsız handler
- **Monorepo:** packages/api içine taşı, tsconfig.base.json'dan extend et

---

*Last updated: 2026-03-02*
