# Flux.1 Dev — Image Generation Prompts

## 🎯 Purpose
Ready-to-use JSON payloads for Flux.1 Dev image generation. Covers the visual assets a frontend developer needs: UI mockups, OG images, app icons, hero illustrations, and style references.

## 📋 Context
- Model: `black-forest-labs/flux-dev` (via fal.ai, Replicate, or ComfyUI)
- Flux Dev does not use `negative_prompt` — describe what you want, not what you don't
- `guidance`: 3.5 is the sweet spot. Lower = more creative, higher = more literal
- `seed`: fix it when you want reproducible results, omit for variation
- Prompt style: natural descriptive sentences work better than comma lists

---

## 1 — UI Mockup / Dashboard Screenshot

### Dark Theme SaaS Dashboard
```json
{
  "prompt": "A high-fidelity screenshot of a modern SaaS analytics dashboard. Dark theme with deep gray background (#111827). Left sidebar with icon navigation. Main area shows three KPI cards at the top with subtle number metrics, below that a full-width area chart with a blue gradient fill showing weekly trends, and below that a data table with avatar columns and status badges. Inter or Geist font. Blue (#3b82f6) as the single accent color. Clean spacing, realistic UI, no lorem ipsum text visible. Professional product screenshot style.",
  "width": 1456,
  "height": 816,
  "num_inference_steps": 28,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

### Light Theme Admin Panel
```json
{
  "prompt": "High-fidelity web application screenshot, light theme admin panel. White background with light gray sidebar. Top navigation bar with breadcrumbs. Main content area shows a kanban board with three columns: To Do, In Progress, Done. Cards have colored category tags, small avatar stacks, and due date labels. Violet (#7c3aed) accent color. Clean, modern, Notion or Linear aesthetic. Realistic UI components, proper shadow depth, no placeholder text.",
  "width": 1456,
  "height": 816,
  "num_inference_steps": 28,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

**Customize:**
- Swap `#111827` / `#3b82f6` for your brand colors
- Replace "analytics dashboard" with your app type (CRM, project management, e-commerce admin)
- Adjust width/height for your target use (1456×816 ≈ 16:9 laptop screen)

---

## 2 — Landing Page Hero Section

### Product / SaaS Hero
```json
{
  "prompt": "Landing page hero section design for a developer tool SaaS product. Clean white background. Left side: large bold headline text block (no actual readable text, just realistic text placeholder shapes), subtitle paragraph, and a primary CTA button in indigo. Right side: a floating browser window mockup showing a dark code editor interface with syntax highlighting in blue and green tones. Subtle grid background pattern. Drop shadow on the browser window. Modern, Vercel or Stripe aesthetic. Wide banner composition, 16:9.",
  "width": 1456,
  "height": 816,
  "num_inference_steps": 30,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

### Mobile App Hero
```json
{
  "prompt": "App landing page hero section. Gradient background from deep violet to indigo. Center-aligned composition. Two floating phone mockups (iPhone-style) slightly angled and overlapping, showing mobile app screens with clean UI. Left phone slightly lower, right phone slightly higher. Both phones cast soft shadows. Below phones: a row of app store badges. Modern gradient light rays in background. Premium feel, no text that is readable.",
  "width": 1456,
  "height": 728,
  "num_inference_steps": 30,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

---

## 3 — OG Image / Social Media Card

### Blog Post / Article Card
```json
{
  "prompt": "Open Graph social media card image. 1200x630 pixels composition. Dark background (#09090b). Left two-thirds: large white bold title text block (placeholder, not readable), small category tag above it in blue, small author name and date below in gray. Right one-third: abstract geometric tech symbol — glowing blue-to-purple gradient, minimal and sharp. A thin horizontal separator line between left and right. Bottom-left: small logo mark placeholder. Professional, modern, matches Vercel or Resend blog style.",
  "width": 1200,
  "height": 630,
  "num_inference_steps": 28,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

### Project / GitHub Repo Card
```json
{
  "prompt": "GitHub social preview image for an open source developer tool. Dark charcoal background. Bold project name in large white monospace font centered in the upper half. Below it: a short one-line description in gray monospace. Lower section: a row of technology tag badges (React, TypeScript, Node.js style labels) in subtle bordered boxes. Bottom corners: small GitHub icon left, star count right. Clean terminal aesthetic with very subtle scanline texture. 1280x640 composition.",
  "width": 1280,
  "height": 640,
  "num_inference_steps": 28,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

---

## 4 — App Icon

### Minimal Geometric Icon
```json
{
  "prompt": "Mobile app icon for iOS and Android. Rounded square shape with smooth corners (superellipse). Deep indigo background (#4f46e5). A single centered abstract symbol: two overlapping thin white circles forming a Venn diagram, representing connection or sync. Flat design, no gradients on the symbol, only the background has a very subtle dark-to-slightly-lighter gradient. No text. No shadows. Clean, works perfectly at both small and large sizes. Isolated on white background.",
  "width": 1024,
  "height": 1024,
  "num_inference_steps": 28,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 100
}
```

### Gradient Glass Icon
```json
{
  "prompt": "Premium iOS app icon. Rounded square with iOS superellipse corners. Background: smooth gradient from electric blue (#2563eb) to violet (#7c3aed). Centered symbol: a clean white lightning bolt or spark shape, slightly translucent glass morphism effect. Subtle inner shadow on the symbol. The overall feel is modern, energetic, premium. No text. Isolated on white. Ultra clean render.",
  "width": 1024,
  "height": 1024,
  "num_inference_steps": 30,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 100
}
```

**Customize:**
- Replace the symbol description with your app concept
- Change background colors to your brand
- For Play Store / App Store, output at 1024×1024

---

## 5 — Hero Illustration / Abstract Visual

### Isometric Developer Scene
```json
{
  "prompt": "Isometric 3D illustration of a developer workspace with no people. Floating objects in an isometric perspective: a laptop showing a code editor, a browser window with a web interface, a terminal window, small floating geometric shapes representing data. Color palette: indigo (#4f46e5), teal (#0d9488), and white on a very light lavender background. Soft shadows, flat shading with minimal gradients. Clean vector-like render. Suitable for a landing page or README hero. No text.",
  "width": 1200,
  "height": 800,
  "num_inference_steps": 30,
  "guidance": 3.5,
  "output_format": "png",
  "output_quality": 95
}
```

### Abstract Tech / Data Visual
```json
{
  "prompt": "Abstract digital art for a tech product background. Dark navy background. Flowing network of glowing nodes and connecting lines, like a neural network or data graph. Nodes pulse with soft blue and violet light. The composition is denser at center and fades toward edges. Subtle particle dust. No text, no UI elements, purely abstract. Cinematic quality, suitable as a hero background image or presentation backdrop.",
  "width": 1456,
  "height": 816,
  "num_inference_steps": 32,
  "guidance": 3.0,
  "output_format": "png",
  "output_quality": 95
}
```

---

## ⚙️ Parameters Reference

| Parameter | Default | Notes |
|-----------|---------|-------|
| `num_inference_steps` | 28 | 20–28 for speed, 30–35 for quality |
| `guidance` | 3.5 | 2.5–3.5 creative, 3.5–5 literal |
| `seed` | omit | Add a number to reproduce the same result |
| `output_format` | `png` | `webp` for smaller file size |
| `output_quality` | 95 | 100 for icons (sharp edges), 90–95 for photos |

**Common Sizes:**
| Use Case | Width | Height |
|----------|-------|--------|
| Dashboard / Laptop screen | 1456 | 816 |
| OG / Social card | 1200 | 630 |
| GitHub social preview | 1280 | 640 |
| App icon | 1024 | 1024 |
| Hero section | 1456 | 728 |
| Square (Instagram) | 1024 | 1024 |

---

## 🔄 Prompt Writing Tips for Flux Dev

- **Lead with the subject:** "A high-fidelity screenshot of..." not "Create an image of..."
- **Name the aesthetic:** "Vercel aesthetic", "Linear.app style", "Stripe landing page feel" — Flux understands these references
- **Specify colors as hex:** `#3b82f6` is more reliable than "bright blue"
- **Say what font:** "Inter font", "monospace font" — helps with any text-like shapes
- **End with the mood:** "Professional product screenshot style" or "Premium, modern feel"
- **Avoid negatives in prompt:** Instead of "no clutter", say "clean, minimal, spacious"

---

*Last updated: 2026-03-02*
