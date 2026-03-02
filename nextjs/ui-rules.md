# Next.js UI Rules

## 🎯 Purpose
UI component patterns specific to Next.js — handling loading states, images, fonts, and the Server/Client component boundary for UI.

## 📋 Context
- Next.js 14+ App Router
- Tailwind CSS
- shadcn/ui as component foundation (or custom primitives)
- Lucide React for icons

## 💬 The Prompt / Rules

```
You are building UI components for a Next.js 14+ App Router project.
Follow these rules for all UI work.

**Server vs Client Component Decision**
Default to Server Component. Add "use client" only when the component:
- Uses useState or useReducer
- Uses useEffect
- Handles browser events (onClick, onChange, etc.)
- Uses browser-only APIs (window, localStorage, etc.)
- Uses a third-party library that requires client context

**Component Architecture**
- One component per file
- Component filename matches the export name: UserCard.tsx → export function UserCard
- Keep components focused — if it does two unrelated things, split it
- Props interface defined above the component, not inline

**Loading States**
- Add loading.tsx for every route that fetches async data
- For partial loading, use <Suspense> with a skeleton fallback
- Skeleton components mirror the shape of the real content — not generic spinners
- Never show an empty div while loading — always show a skeleton or loading indicator

**Images**
- Always use next/image — never <img>
- Set width and height explicitly, or use fill with a sized container
- Add alt text — never alt=""
- Use sizes prop for responsive images
- Lazy load by default (loading="lazy" is the default) — only override for above-the-fold hero images with priority

**Fonts**
- Always use next/font — never link tags or @import in CSS
- Define fonts in app/fonts.ts, apply via className on <html> in layout.tsx
- Use CSS variables for font families: --font-sans, --font-mono

**Icons**
- Use lucide-react — consistent with shadcn/ui
- Import only the icons you use — no wildcard imports
- Size via className: <Icon className="h-4 w-4" />
- Always add aria-label to icon-only buttons

**Accessibility**
- All interactive elements are keyboard accessible
- Modals trap focus and restore it on close
- Form inputs always have a visible label (not just placeholder)
- Color is never the only way to convey information
- Use semantic HTML: <button> for actions, <a> for navigation

**shadcn/ui Usage**
- Prefer shadcn/ui primitives over building from scratch
- Do not modify files in components/ui/ directly — extend via composition
- To customize behavior, wrap the shadcn component in your own component

**Responsive Design**
- Mobile-first: write base styles for mobile, add breakpoints for larger screens
- Test at 375px (mobile), 768px (tablet), 1280px (desktop)
- Avoid fixed pixel widths on containers — use max-w- with w-full
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{component_name}` | Component being built | `UserAvatar` |
| `{breakpoint}` | Target breakpoint | `md`, `lg` |

## 📝 Example Output

```typescript
// components/UserCard.tsx — Server Component
import Image from 'next/image'
import { MapPinIcon } from 'lucide-react'

interface UserCardProps {
  name: string
  avatarUrl: string
  location: string
}

export function UserCard({ name, avatarUrl, location }: UserCardProps) {
  return (
    <div className="flex items-center gap-3 rounded-lg border p-4">
      <Image src={avatarUrl} alt={name} width={40} height={40} className="rounded-full" />
      <div>
        <p className="font-medium">{name}</p>
        <p className="flex items-center gap-1 text-sm text-muted-foreground">
          <MapPinIcon className="h-3 w-3" />
          {location}
        </p>
      </div>
    </div>
  )
}
```

## 🔄 Variations

- **Without shadcn/ui:** Use Radix UI primitives directly
- **Animation:** Add Framer Motion rules for transition patterns

---

*Last updated: 2026-03-02*
