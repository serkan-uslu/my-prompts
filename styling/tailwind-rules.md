# Tailwind CSS Rules

## 🎯 Purpose
Consistent Tailwind CSS usage patterns that prevent class sprawl, maintain readability, and produce predictable responsive layouts.

## 📋 Context
- Tailwind CSS v3+
- React/Next.js with JSX
- Using `cn()` utility (clsx + tailwind-merge)

## 💬 The Prompt / Rules

```
You are styling a React/Next.js application with Tailwind CSS.
Follow these conventions for all styling work.

**Core Principles**
- Utility-first: build UI from utility classes, not custom CSS
- No inline styles ever
- No CSS modules unless Tailwind genuinely can't do it
- No @apply except for 3rd party overrides (and with a comment explaining why)

**Class Organization (in this order)**
1. Layout: display, position, z-index
2. Box model: w, h, m, p, border
3. Typography: font, text, leading, tracking
4. Colors: bg, text color, border-color
5. Effects: shadow, opacity, transition
6. State variants: hover, focus, disabled
7. Responsive: sm:, md:, lg:, xl:

// Example:
className="flex flex-col gap-4 w-full max-w-md p-6 bg-white rounded-lg shadow-sm hover:shadow-md transition-shadow"

**The cn() Utility — Always Use for Conditional Classes**
import { cn } from '@/lib/utils'

// Good — readable, handles conflicts
className={cn(
  "flex items-center gap-2 px-4 py-2 rounded-md text-sm font-medium",
  isActive && "bg-primary text-primary-foreground",
  isDisabled && "opacity-50 cursor-not-allowed",
  className  // allow override from parent
)}

// Bad — string concatenation breaks Tailwind IntelliSense and has merging bugs
className={`flex ${isActive ? 'bg-primary' : ''}`}

**Responsive Design — Mobile First**
- Write base styles for mobile (no prefix)
- Add sm: for 640px+, md: for 768px+, lg: for 1024px+, xl: for 1280px+
- Don't skip sizes — go mobile → tablet → desktop, not just mobile → desktop

**Color Usage**
- Use semantic color variables, not raw Tailwind colors:
  bg-background, text-foreground, bg-primary, text-muted-foreground
- Don't use literal colors (bg-blue-500) for theme colors — they don't support dark mode
- Use arbitrary values sparingly: bg-[#custom] only for brand colors not in the palette

**Spacing Scale**
- Stick to the Tailwind scale — don't use arbitrary pixel values for common spacing
- p-4 = 16px, p-6 = 24px, p-8 = 32px — memorize these
- gap-* for flex/grid gaps, space-* for spacing between siblings in a stack

**Typography**
- text-sm (14px), text-base (16px), text-lg (18px), text-xl (20px), text-2xl (24px)
- font-medium for labels, font-semibold for headings, font-bold sparingly
- text-muted-foreground for secondary text (not text-gray-500 — breaks dark mode)

**Component Variants**
When a component has multiple visual states, define a variants object:
const variants = {
  default: "bg-primary text-primary-foreground",
  outline: "border border-input bg-transparent",
  ghost: "hover:bg-accent hover:text-accent-foreground",
}
// Apply with: variants[variant]

Not with: if (variant === 'default') className = '...'

**Dark Mode**
- Use dark: prefix, not JS-based switching
- Background: bg-background (automatically adapts)
- Never hard-code colors for elements visible in dark mode

**What to Extract into a Component**
Extract if you repeat the same class combination 3+ times:
<Card> instead of <div className="rounded-lg border p-6 shadow-sm"> everywhere
Don't extract if used once or twice — inline it.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{breakpoint}` | Target breakpoint | `md`, `lg` |
| `{semantic_color}` | Color token | `bg-primary`, `text-destructive` |

## 📝 Example Output

```tsx
// Button component with proper Tailwind patterns
export function Button({
  variant = 'default',
  size = 'md',
  className,
  ...props
}: ButtonProps) {
  return (
    <button
      className={cn(
        "inline-flex items-center justify-center rounded-md font-medium transition-colors",
        "focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring",
        "disabled:opacity-50 disabled:pointer-events-none",
        {
          default: "bg-primary text-primary-foreground hover:bg-primary/90",
          outline: "border border-input bg-transparent hover:bg-accent",
        }[variant],
        { sm: "h-8 px-3 text-xs", md: "h-10 px-4 text-sm", lg: "h-11 px-6" }[size],
        className
      )}
      {...props}
    />
  )
}
```

## 🔄 Variations

- **With Tailwind v4:** Update color system to CSS variable-based approach
- **Design Tokens:** Add a note on mapping Figma tokens to Tailwind config

---

*Last updated: 2026-03-02*
