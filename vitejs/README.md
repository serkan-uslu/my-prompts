# Vite.js Rules

Rules for Vite-based React projects (SPA ve SSG).

## Files

| File | Purpose | Use When |
|------|---------|----------|
| `project-init-rules.md` | Yeni proje scaffold | Vite ile React projesi başlatırken |
| `development-rules.md` | Günlük geliştirme kuralları | Her Vite/React session'ında |
| `build-rules.md` | Bundle optimizasyonu | Build, deploy, perf tuning yaparken |

## Next.js'e Göre Farklar

| Konu | Vite (SPA) | Next.js |
|------|-----------|---------|
| Routing | React Router v6 | App Router (file-based) |
| Data Fetching | TanStack Query (client) | Server Components + fetch |
| SSR | Vite SSR (manuel) | Built-in |
| Auth | Client-side redirect | Middleware redirect |

## Önerilen Kombinasyonlar

**Full SPA:**
```
vitejs/development-rules.md
+ code-quality/component-rules.md
+ state-and-data/data-fetching-rules.md
```

**Admin panel / Dashboard:**
```
vitejs/development-rules.md
+ state-and-data/state-management-rules.md
+ styling/tailwind-rules.md
```

---

*Last updated: 2026-03-02*
