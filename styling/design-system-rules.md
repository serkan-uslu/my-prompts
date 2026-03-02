# Design System Rules

## 🎯 Purpose
Rules for building and maintaining a consistent design system — tokens, primitives, and component library patterns. Prevents design inconsistency from accumulating.

## 📋 Context
- Tailwind CSS with custom theme configuration
- shadcn/ui as the primitive component layer
- Design tokens sourced from Figma or defined in tailwind.config.ts

## 💬 The Prompt / Rules

```
You are building and maintaining a design system for a React application.
Follow these rules for all design system work.

**Design Token Hierarchy**
Primitive tokens → Semantic tokens → Component tokens

Primitive: specific values
  --color-blue-500: #3b82f6

Semantic: purpose-based, reference primitives
  --color-primary: var(--color-blue-500)
  --color-background: var(--color-white)

Component: component-specific
  --button-primary-bg: var(--color-primary)

In tailwind.config.ts, define semantic tokens only — not primitive colors.

**Color System**
Required semantic colors (maps to CSS variables):
- background / foreground — page background and default text
- primary / primary-foreground — brand color, used for CTAs
- secondary / secondary-foreground — secondary actions
- muted / muted-foreground — subdued backgrounds and hint text
- accent / accent-foreground — highlights, hover states
- destructive / destructive-foreground — errors, delete actions
- border — default border color
- ring — focus ring color

Support dark mode from the start by defining both light and dark values in :root and .dark.

**Typography Scale**
Define a clear type scale — don't let it grow organically:
- xs: 12px — metadata, labels
- sm: 14px — body small, secondary text
- base: 16px — body text
- lg: 18px — large body
- xl: 20px — small headings
- 2xl: 24px — section headings
- 3xl: 30px — page headings
- 4xl: 36px — display

**Spacing Scale**
Use Tailwind's default 4px base scale — don't add custom spacing values unless required by the design.

**Component Primitive Rules**
- Primitives wrap Radix UI or HTML elements
- They accept className for extension
- They have consistent size variants: sm, md, lg
- They have consistent state variants: default, destructive, outline, ghost
- They never contain business logic
- They never fetch data

**Component Layer Hierarchy**
Primitives (Button, Input, Modal)
  ↓ composed into
Domain Components (UserCard, PaymentForm)
  ↓ assembled into
Pages (app/dashboard/page.tsx)

Never skip layers — page components should not use primitives directly for complex UI.

**Documentation**
Every component in the design system needs:
- Usage example (code snippet)
- Prop table
- Visual states listed

For shadcn/ui-based projects, this comes for free. For custom components, document at minimum the variants.

**Adding to the Design System**
Before adding a new component:
1. Check if shadcn/ui has it — prefer existing primitives
2. Check if the pattern exists elsewhere in the codebase — unify instead of add
3. If adding: define all states (loading, disabled, error) before implementing
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{brand_color}` | Primary brand color | `#6366f1` (indigo) |
| `{font_family}` | Brand font | `Inter`, `Geist` |

## 📝 Example Output

```typescript
// tailwind.config.ts — semantic color tokens
colors: {
  background: 'hsl(var(--background))',
  foreground: 'hsl(var(--foreground))',
  primary: {
    DEFAULT: 'hsl(var(--primary))',
    foreground: 'hsl(var(--primary-foreground))',
  },
  muted: {
    DEFAULT: 'hsl(var(--muted))',
    foreground: 'hsl(var(--muted-foreground))',
  },
  destructive: {
    DEFAULT: 'hsl(var(--destructive))',
    foreground: 'hsl(var(--destructive-foreground))',
  },
}
```

## 🔄 Variations

- **With Figma Tokens:** Add step for exporting Figma tokens and mapping to CSS variables
- **Multi-brand:** Add theme switching by swapping CSS variable sets

---

*Last updated: 2026-03-02*
