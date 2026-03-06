---
name: dark-theme
description: Dark theme design system for developer-focused marketing and product sites
when_to_use: When building any dark-themed website, especially developer tools, SaaS, or technical products
---

# Dark Theme — Design System

## Quick Reference

**Vibe:** Premium, technical, confident. Think Linear, Vercel, Raycast, not "dark mode toggle."
**Background:** Near-black, not pure black. `#0a0a0a` to `#111111`, never `#000000`.
**Text:** Off-white primary, muted secondary. Never pure white `#ffffff` on dark backgrounds (too harsh).
**Accent:** One strong brand color. Used sparingly for CTAs, highlights, interactive elements.

## Color System

### Base Palette (CSS Variables)

```css
:root {
  /* Backgrounds — layered depth */
  --background:        0 0% 4%;        /* #0a0a0a — deepest layer */
  --background-subtle: 0 0% 7%;        /* #121212 — cards, sections */
  --background-muted:  0 0% 10%;       /* #1a1a1a — hover states, wells */
  
  /* Borders — barely visible structure */
  --border:            0 0% 15%;       /* #262626 — default borders */
  --border-subtle:     0 0% 12%;       /* #1f1f1f — subtle dividers */
  --border-hover:      0 0% 20%;       /* #333333 — interactive borders */
  
  /* Text — hierarchy through opacity, not color */
  --foreground:        0 0% 93%;       /* #ededed — primary text */
  --foreground-muted:  0 0% 60%;       /* #999999 — secondary text */
  --foreground-subtle: 0 0% 40%;       /* #666666 — tertiary, captions */
  
  /* Accent — your brand color (customize per project) */
  --primary:           210 100% 50%;   /* Blue default — change this */
  --primary-foreground: 0 0% 100%;     /* Text on primary buttons */
}
```

### Depth Through Layering

Dark themes create depth by stacking slightly lighter surfaces. Each layer up = slightly lighter background.

```
Layer 0: --background        (page)
Layer 1: --background-subtle (cards, nav, sidebar)
Layer 2: --background-muted  (dropdowns, tooltips, hover)
Layer 3: --border            (borders define edges between layers)
```

**Never use shadows for depth in dark themes.** Shadows disappear against dark backgrounds. Use border + background layering instead.

```tsx
// ✅ Dark theme depth
<Card className="border-border/50 bg-background-subtle">

// ❌ Shadow-based depth (invisible on dark)
<Card className="shadow-lg bg-background">
```

### Gradients

Subtle gradients add premium feel. Keep them nearly imperceptible.

```css
/* Hero background gradient — radial glow behind content */
.hero-gradient {
  background: radial-gradient(
    ellipse 80% 50% at 50% -20%,
    hsl(var(--primary) / 0.15),
    transparent
  );
}

/* Section divider — fade between sections */
.section-fade {
  background: linear-gradient(
    to bottom,
    hsl(var(--background)),
    hsl(var(--background-subtle))
  );
}

/* Accent glow behind CTAs or hero elements */
.accent-glow {
  background: radial-gradient(
    circle at center,
    hsl(var(--primary) / 0.2) 0%,
    transparent 70%
  );
}
```

## Typography

### Hierarchy

```css
/* Display — hero headlines only */
.display    { font-size: 3.5rem; line-height: 1.1; font-weight: 700; letter-spacing: -0.02em; }

/* Headings */
h1          { font-size: 2.5rem; line-height: 1.2; font-weight: 700; letter-spacing: -0.02em; }
h2          { font-size: 2rem;   line-height: 1.25; font-weight: 600; letter-spacing: -0.01em; }
h3          { font-size: 1.25rem; line-height: 1.4; font-weight: 600; }

/* Body */
.body       { font-size: 1rem;   line-height: 1.6; color: hsl(var(--foreground-muted)); }
.body-sm    { font-size: 0.875rem; line-height: 1.5; color: hsl(var(--foreground-subtle)); }

/* Code */
code        { font-family: 'Geist Mono', monospace; font-size: 0.875em; }
```

### Font Pairing

**Recommended:** Geist (Sans) + Geist Mono. Clean, technical, Vercel-native.
**Alternative:** Inter + JetBrains Mono. Universally available, excellent readability.

```tsx
import { GeistSans } from "geist/font/sans"
import { GeistMono } from "geist/font/mono"

<html className={`${GeistSans.variable} ${GeistMono.variable}`}>
```

### Text Rendering

```css
body {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
}
```

## Component Patterns

### Cards

```tsx
// Standard card — subtle border, elevated background
<div className="rounded-xl border border-border/50 bg-background-subtle p-6">

// Interactive card — hover state with border brightening
<div className="rounded-xl border border-border/50 bg-background-subtle p-6 
                transition-colors hover:border-border-hover hover:bg-background-muted">

// Featured card — accent border or glow
<div className="rounded-xl border border-primary/20 bg-background-subtle p-6
                shadow-[0_0_15px_rgba(var(--primary),0.1)]">
```

### Buttons

```tsx
// Primary — solid accent color
<Button className="bg-primary text-primary-foreground hover:bg-primary/90">
  Get started
</Button>

// Secondary — outline on dark
<Button variant="outline" className="border-border hover:bg-background-muted">
  Learn more
</Button>

// Ghost — minimal, text-only feel
<Button variant="ghost" className="text-foreground-muted hover:text-foreground">
  Documentation
</Button>
```

### Navigation

```tsx
<header className="sticky top-0 z-50 border-b border-border/50 bg-background/80 backdrop-blur-md">
  <nav className="container mx-auto flex h-16 items-center justify-between px-4">
    {/* Logo */}
    <Link href="/" className="text-lg font-semibold tracking-tight">
      Brand
    </Link>
    
    {/* Nav links — muted, brighten on hover */}
    <div className="hidden items-center gap-8 md:flex">
      <Link className="text-sm text-foreground-muted transition-colors hover:text-foreground">
        Features
      </Link>
    </div>
    
    {/* CTA */}
    <Button size="sm">Get started</Button>
  </nav>
</header>
```

### Code Blocks

```tsx
// Syntax-highlighted code block with copy button
<div className="relative rounded-lg border border-border/50 bg-[#0d0d0d]">
  <div className="flex items-center justify-between border-b border-border/50 px-4 py-2">
    <span className="text-xs text-foreground-subtle">app/page.tsx</span>
    <Button variant="ghost" size="sm" className="h-6 text-xs">Copy</Button>
  </div>
  <pre className="overflow-x-auto p-4">
    <code className="text-sm font-mono">{code}</code>
  </pre>
</div>
```

### Section Spacing

```tsx
// Standard section
<section className="py-24 md:py-32">

// Section with subtle background shift
<section className="bg-background-subtle py-24 md:py-32">

// Section with top gradient fade
<section className="relative py-24 md:py-32">
  <div className="absolute inset-x-0 top-0 h-px bg-gradient-to-r from-transparent via-border to-transparent" />
</section>
```

## Visual Effects

### Glassmorphism (Use Sparingly)

```css
.glass {
  background: hsl(var(--background-subtle) / 0.5);
  backdrop-filter: blur(12px);
  border: 1px solid hsl(var(--border) / 0.5);
}
```

### Grid/Dot Backgrounds

```css
/* Subtle dot grid — barely visible texture */
.dot-grid {
  background-image: radial-gradient(
    circle,
    hsl(var(--foreground-subtle) / 0.15) 1px,
    transparent 1px
  );
  background-size: 24px 24px;
}

/* Line grid */
.line-grid {
  background-image: 
    linear-gradient(hsl(var(--border) / 0.3) 1px, transparent 1px),
    linear-gradient(90deg, hsl(var(--border) / 0.3) 1px, transparent 1px);
  background-size: 64px 64px;
}
```

### Glow Effects

```css
/* Element glow — use on hero images, featured cards */
.glow {
  box-shadow: 
    0 0 20px hsl(var(--primary) / 0.15),
    0 0 60px hsl(var(--primary) / 0.1);
}

/* Text glow — headlines only, very subtle */
.text-glow {
  text-shadow: 0 0 40px hsl(var(--primary) / 0.3);
}
```

## Anti-patterns

- ❌ Pure black `#000000` backgrounds (too flat, no depth)
- ❌ Pure white `#ffffff` text (too harsh, causes eye strain)
- ❌ Box shadows for depth (invisible on dark backgrounds)
- ❌ Colored backgrounds for sections (use layered neutrals)
- ❌ Light theme color palette with inverted values (dark themes need their own design)
- ❌ Low contrast text (WCAG AA minimum: 4.5:1 for body, 3:1 for large text)
- ❌ Too many accent colors (one primary, one secondary max)
- ❌ Heavy glassmorphism everywhere (it's an accent, not a system)
- ❌ Bright borders (keep borders subtle, `border-border/50`)
- ❌ Neon/saturated colors at full brightness (desaturate slightly for dark backgrounds)

## Gotchas

1. **Test on multiple screens.** Dark themes look wildly different on IPS vs OLED vs TN panels. What looks subtle on your monitor might be invisible on a cheap display.
2. **Images need treatment.** Photos on dark backgrounds need a subtle border or overlay. Raw images float awkwardly without containment.
3. **Contrast checker is mandatory.** Use WebAIM or built-in browser tools. Dark themes fail accessibility more often than light themes.
4. **Favicon needs light variant.** Your dark favicon will disappear on dark browser themes. Provide both.
5. **OG images need dark treatment too.** Social cards render on white backgrounds (Twitter, Slack). Your dark OG image needs enough contrast to read on light surfaces.
6. **Print styles.** Dark themes print terribly. If anyone might print the page, add `@media print` overrides.
7. **Scrollbar styling.** Default browser scrollbars look jarring on dark themes. Style them: `scrollbar-color: hsl(var(--border)) transparent`.
