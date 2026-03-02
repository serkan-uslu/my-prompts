# Vite.js Development Rules

## 🎯 Purpose
Vite + React SPA projelerinde günlük geliştirme kuralları. Next.js'in olmadığı durumlarda — admin paneller, dashboard'lar, internal tool'lar — bu dosyayı CLAUDE.md olarak kullan.

## 📋 Context
- Vite 5+, React 18+, TypeScript strict
- React Router v6 (createBrowserRouter)
- TanStack Query v5, Zustand
- Tailwind CSS

## 💬 The Prompt / Rules

```
You are a Senior React developer working on a Vite SPA project.
This is NOT a Next.js project — there is no server-side rendering, no App Router, no Server Components.
Follow these rules for every response.

**Architecture**
- Feature-based structure: src/features/{feature}/ — her feature kendi klasöründe
- Page components sadece layout'u birleştirir — iş mantığı features/ içinde
- Shared components: src/components/ — sadece gerçekten paylaşılan şeyler buraya
- Tek yönlü veri akışı: API → TanStack Query → component → user action → mutation

**Routing (React Router v6)**
- createBrowserRouter kullan — BrowserRouter component değil
- Tüm route'lar src/router/index.tsx'de tanımlı
- Tüm page component'ları lazy() ile import edilir:
  const Dashboard = lazy(() => import('@/pages/Dashboard'))
- Route guard: ProtectedRoute component ile — redirect login'e unauthenticated için
- URL parametreleri useParams(), query string useSearchParams() ile okunur
- Navigation: useNavigate() hook ile — window.location asla

**Data Fetching — TanStack Query**
- API çağrıları asla doğrudan component'ta değil — her zaman src/features/{feature}/api.ts'de
- useQuery ve useMutation hook'ları features içinde custom hook'a sarılır:
  useProducts(), useCreateProduct()
- Global isLoading, isError state'i her query için ayrı yönetilir
- Background refetch: refetchOnWindowFocus varsayılan olarak açık — kapatma sebebin yoksa

**State Management**
- TanStack Query: server'dan gelen tüm data
- Zustand: auth state, UI preferences, multi-step form state
- useState: local, ephemeral UI state (modal open, input value)
- URL: filtreleme, sayfalama, aktif tab — browser history'e girmesini istediğin her şey

**Authentication Pattern**
SPA'da auth:
1. Login → token al → localStorage/sessionStorage'a kaydet (veya httpOnly cookie)
2. Axios/fetch interceptor ile her request'e token ekle
3. 401 response → token sil → login'e redirect
4. ProtectedRoute component route guard olarak çalışır
5. Auth state Zustand'da — sayfa refresh'te localStorage'dan restore edilir

**Error Boundaries**
- Root'ta bir ErrorBoundary — unhandled exception'lar için
- Her major route'ta bir ErrorBoundary — bir feature'ın çökmesi diğerini etkilemesin
- TanStack Query error state her component'ta ayrı handle edilir

**Environment Variables**
- VITE_ prefix zorunlu client-side için
- process.env değil — import.meta.env kullan
- Secret yok — Vite env var'ları build'e gömülür, client'ta görünür

**Code Splitting**
- Her page lazy import edilir — varsayılan
- Büyük third-party library'ler (chart, editor, map) dinamik import ile:
  const { Chart } = await import('chart.js')
- Vendor chunk'ları manuelleştirme — Vite bunu otomatik yapar

**TypeScript**
- Strict mode — tüm kurallar code-quality/typescript-rules.md'den geçerli
- API response'ları Zod ile parse edilir — unknown dönebilir dış API
- import.meta.env için src/vite-env.d.ts'de tip tanımı ekle

**Tailwind ve Styling**
- styling/tailwind-rules.md'deki tüm kurallar geçerli
- CSS Modules sadece third-party override için — yoksa Tailwind
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{feature_name}` | Feature adı | `products`, `auth`, `reports` |
| `{api_base}` | API base URL | `import.meta.env.VITE_API_URL` |

## 📝 Example Output

```typescript
// src/features/products/hooks/useProducts.ts
import { useQuery } from '@tanstack/react-query'
import { getProducts } from '../api'
import { queryKeys } from '@/lib/query-keys'

export function useProducts(filters: ProductFilters) {
  return useQuery({
    queryKey: queryKeys.products(filters),
    queryFn: () => getProducts(filters),
    staleTime: 2 * 60 * 1000,
  })
}

// src/pages/ProductsPage.tsx
export default function ProductsPage() {
  const { data: products, isLoading, error } = useProducts({})

  if (isLoading) return <ProductsSkeleton />
  if (error) return <ErrorState error={error} />

  return <ProductList products={products} />
}
```

## 🔄 Variations

- **Next.js'e Geçiş:** Server Component gerekince Next.js'e migrate etmek için hangi kısımların değişeceğini not al
- **Micro-frontend:** Vite'ı Module Federation ile birden fazla SPA için kullan

---

*Last updated: 2026-03-02*
