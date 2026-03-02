# Vite.js Project Initialization Rules

## 🎯 Purpose
Yeni bir Vite + React + TypeScript projesini doğru yapıyla scaffold etmek. İlk günden itibaren teknik borç biriktirmeden başlamak.

## 📋 Context
- Vite 5+, React 18+, TypeScript strict
- SPA (Single Page Application) — Next.js gerekmediğinde
- Tailwind CSS + React Router v6 + TanStack Query

## 💬 The Prompt / Rules

```
You are setting up a new Vite + React + TypeScript project called {project_name}.
Follow these initialization rules. Do not add any feature code — only the scaffold.

**Project Creation**
npm create vite@latest {project_name} -- --template react-ts
cd {project_name}

**Required Dependencies — Install All**
Core:
  npm install react-router-dom @tanstack/react-query zustand zod

Styling:
  npm install -D tailwindcss postcss autoprefixer
  npx tailwindcss init -p

Utilities:
  npm install clsx tailwind-merge lucide-react

Dev:
  npm install -D @types/node eslint-plugin-react-hooks

**Directory Structure — Create Before Writing Features**
src/
  assets/          — static files (images, fonts, svgs)
  components/
    ui/            — primitive components (Button, Input, Modal)
    layout/        — structural components (Header, Sidebar)
  features/        — feature-based modules
    {feature}/
      components/  — feature-specific components
      hooks/       — feature-specific hooks
      api.ts       — API calls for this feature
      types.ts     — TypeScript types for this feature
  hooks/           — shared custom hooks
  lib/             — utilities, helpers
    utils.ts       — cn() and other helpers
  pages/           — route-level page components
  router/          — React Router config
  store/           — Zustand stores
  types/           — global shared types
  main.tsx         — entry point
  App.tsx          — root component with providers

**vite.config.ts — Essential Config**
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
})

**tsconfig.json — Add Path Alias**
Add to compilerOptions:
"baseUrl": ".",
"paths": {
  "@/*": ["./src/*"]
}

**React Router Setup (src/router/index.tsx)**
Use createBrowserRouter — not BrowserRouter component.
Define all routes in one place.
Use lazy() for page components — all pages are code-split by default.

**TanStack Query Setup (src/main.tsx)**
Wrap app with QueryClientProvider.
QueryClient config:
  defaultOptions: {
    queries: {
      staleTime: 60 * 1000,
      retry: 1,
    }
  }

**lib/utils.ts — Create First**
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

**Environment Variables**
Create .env.local:
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME={project_name}

Create .env.example with same keys but empty values.
Note: VITE_ prefix required for client-side env vars.
Note: env vars are inlined at build time — not truly secret.
Never put secrets in Vite env vars.

**ESLint**
Keep the default Vite ESLint config.
Add: "plugin:react-hooks/recommended" to extends.

**Git**
- Add dist/ to .gitignore (should already be there)
- First commit: "chore: initial Vite + React project setup"

After setup, list what was created and what still needs configuration.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{project_name}` | Proje adı | `admin-dashboard` |
| `{api_url}` | Backend API adresi | `http://localhost:3001/api` |

## 📝 Example Output

```
my-app/
├── src/
│   ├── components/ui/     ← hazır, boş
│   ├── features/          ← feature'lar buraya gelecek
│   ├── lib/utils.ts       ← cn() hazır
│   ├── router/index.tsx   ← createBrowserRouter kurulu
│   └── main.tsx           ← QueryClientProvider + RouterProvider
├── .env.local             ← placeholder'lar
├── .env.example           ← commit'lenecek
└── vite.config.ts         ← @ alias kurulu
```

## 🔄 Variations

- **Minimal SPA:** Store ve TanStack Query olmadan — sadece React Router + Tailwind
- **Monorepo:** Vite config'e `root` ve `build.outDir` ekle
- **Library Mode:** `build.lib` config ile component kütüphanesi olarak publish etmek için

---

*Last updated: 2026-03-02*
