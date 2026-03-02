# Vite.js Build & Optimization Rules

## 🎯 Purpose
Vite build sürecini optimize etmek, bundle boyutunu küçültmek ve production deployment kurallarını tanımlamak.

## 📋 Context
- Vite 5+
- Production deploy: Vercel, Netlify, veya static hosting
- Bundle analizi için rollup-plugin-visualizer

## 💬 The Prompt / Rules

```
You are optimizing the build of a Vite + React application.
Follow these rules for build configuration and performance work.

**vite.config.ts — Production Build Config**
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: { '@': path.resolve(__dirname, './src') },
  },
  build: {
    target: 'es2020',
    sourcemap: true,              // production'da hata ayıklama için
    chunkSizeWarningLimit: 500,   // 500KB üzeri uyarı ver
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          query: ['@tanstack/react-query'],
          ui: ['lucide-react'],
        },
      },
    },
  },
})

**Code Splitting Stratejisi**
- Her route lazy() ile import edilmeli — pages/ klasöründeki her dosya
- Heavy library'ler (chart, editor, PDF, map) dinamik import:
  const { default: Chart } = await import('chart.js/auto')
- manualChunks: vendor, query, ui gibi büyük ve ortak dependency'ler ayrı chunk'a

**Bundle Boyutu Kuralları**
- Initial bundle (vendor hariç): < 200KB gzipped hedef
- Her route chunk: < 100KB gzipped hedef
- Büyük ikonlar: lucide-react'ten sadece kullanılan ikonları import et (tree-shaking otomatik)
- Moment.js varsa → date-fns ile değiştir (6x daha küçük)
- Lodash tam import varsa → named import kullan: import { debounce } from 'lodash-es'

**Bundle Analizi**
npm install -D rollup-plugin-visualizer

vite.config.ts'ye ekle:
import { visualizer } from 'rollup-plugin-visualizer'
plugins: [react(), visualizer({ open: true })]

Build sonrası stats.html açılır — neyin büyük olduğunu görmek için.

**Tree Shaking**
Vite/Rollup tree shaking varsayılan açık. Engelleme:
- Side effect içeren barrel file'lar (index.ts her şeyi export ediyorsa)
- CommonJS modules (ESM kullan)
- "sideEffects": false ekle package.json'a kendi paketlerin için

**Environment Separation**
.env.development — lokal geliştirme
.env.staging — staging ortamı
.env.production — production

Build komutları:
"build:staging": "vite build --mode staging"
"build:prod": "vite build --mode production"

**Asset Optimizasyonu**
- SVG'ler: SVGR ile React component olarak import et (inline, tree-shaken)
- Resimler: WebP formatı, Vite asset pipeline ile otomatik hash'lenir
- Font'lar: public/ yerine src/assets/ — bundle'a dahil olur, cache'lenir
- 4KB altı assets otomatik base64 inline edilir — küçük ikonlar için iyi

**Performance Checklist (Build Öncesi)**
☐ Bundle analyzer çalıştır, büyük dependency'leri kontrol et
☐ Tüm page component'ları lazy import mi?
☐ Heavy library'ler dinamik import mi?
☐ Kullanılmayan import var mı? (ESLint no-unused-vars kontrol eder)
☐ Console.log'lar kaldırıldı mı? (esbuild drop: ['console'] eklenebilir)
☐ Source map production'da mı? (evet — hata ayıklama için)

**Deployment**
- dist/ klasörünü deploy et
- SPA routing için: tüm istekleri index.html'e yönlendir
  Vercel: vercel.json'da rewrites
  Netlify: _redirects dosyasında /* /index.html 200
  Nginx: try_files $uri /index.html
- Cache headers:
  index.html: no-cache (her deploy'da güncellenmeli)
  assets (hash'li): max-age=31536000 immutable (hash değişince yeni URL)
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{bundle_target}` | Target bundle boyutu | `< 200KB gzipped` |
| `{deploy_platform}` | Deploy ortamı | `Vercel`, `Netlify`, `AWS S3` |

## 📝 Example Output

```typescript
// vite.config.ts — production-ready
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig(({ mode }) => ({
  plugins: [react()],
  resolve: {
    alias: { '@': path.resolve(__dirname, './src') },
  },
  build: {
    target: 'es2020',
    sourcemap: mode === 'production',
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          query: ['@tanstack/react-query'],
        },
      },
    },
  },
}))
```

```
// public/_redirects (Netlify SPA routing)
/* /index.html 200
```

## 🔄 Variations

- **Vitest:** `test: { environment: 'jsdom' }` ekle vite.config.ts'e, Jest yerine Vitest kullan
- **Library Build:** `build.lib` config ile npm'e publish edilecek component kütüphanesi

---

*Last updated: 2026-03-02*
