---
name: frontend
description: Next.js 15 + App Router + shadcn/ui + Tailwind v4 patterns for production web apps
when_to_use: When building any web application with the Next.js + shadcn + Tailwind stack
---

# Frontend — Next.js 15 + shadcn/ui + Tailwind v4

## Quick Reference

**Stack:** Next.js 15 (App Router) / React 19 / TypeScript / Tailwind v4 / shadcn/ui
**Package manager:** pnpm
**Styling:** Tailwind utility classes + CSS variables for theming. No CSS modules, no styled-components.
**Components:** shadcn/ui as the base. Extend, don't replace.

## File Structure

```
app/
├── layout.tsx              # Root layout (fonts, providers, metadata)
├── page.tsx                # Homepage
├── (marketing)/            # Route group: public pages (no auth)
│   ├── layout.tsx
│   ├── about/page.tsx
│   └── pricing/page.tsx
├── (dashboard)/            # Route group: auth-gated pages
│   ├── layout.tsx
│   └── settings/page.tsx
└── api/                    # Route handlers (API endpoints)
    └── webhook/route.ts
components/
├── ui/                     # shadcn/ui primitives (button, card, dialog, etc.)
├── sections/               # Page sections (hero, features, cta, etc.)
├── layout/                 # Layout components (navbar, footer, sidebar)
└── shared/                 # Reusable composed components
lib/
├── utils.ts                # cn() helper, formatters
└── constants.ts            # Site-wide constants
```

## Patterns

### Server vs Client Components

**Default to Server Components.** Only add `"use client"` when you need:
- Event handlers (onClick, onChange, onSubmit)
- useState, useEffect, useRef
- Browser APIs (window, document, localStorage)
- Third-party client libraries

```tsx
// ✅ Server Component (default) — no directive needed
export default function Hero() {
  return (
    <section className="py-24">
      <h1 className="text-5xl font-bold">Ship faster</h1>
    </section>
  )
}

// ✅ Client Component — only when interactive
"use client"
export function MobileNav() {
  const [open, setOpen] = useState(false)
  return <Sheet open={open} onOpenChange={setOpen}>...</Sheet>
}
```

### Component Conventions

```tsx
// Props interface, not type alias
interface HeroProps {
  title: string
  subtitle?: string
  cta?: { label: string; href: string }
}

// Named export for components, default export for pages
export function Hero({ title, subtitle, cta }: HeroProps) {
  return (
    <section className="relative py-24 md:py-32">
      <div className="container mx-auto px-4 md:px-6">
        <h1 className="text-4xl font-bold tracking-tight sm:text-5xl md:text-6xl">
          {title}
        </h1>
        {subtitle && (
          <p className="mt-6 max-w-2xl text-lg text-muted-foreground">
            {subtitle}
          </p>
        )}
        {cta && (
          <Button asChild size="lg" className="mt-8">
            <Link href={cta.href}>{cta.label}</Link>
          </Button>
        )}
      </div>
    </section>
  )
}
```

### Responsive Design

Mobile-first. Always. Use Tailwind breakpoints: `sm:` (640px), `md:` (768px), `lg:` (1024px), `xl:` (1280px).

```tsx
// ✅ Mobile-first responsive
<div className="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-3">

// ✅ Hide/show by breakpoint
<nav className="hidden md:flex">      {/* Desktop nav */}
<Sheet className="md:hidden">         {/* Mobile nav */}

// ✅ Responsive typography
<h1 className="text-3xl sm:text-4xl md:text-5xl lg:text-6xl">

// ✅ Responsive spacing
<section className="py-16 md:py-24 lg:py-32">
```

### Layout Patterns

```tsx
// Centered container with max-width
<div className="container mx-auto px-4 md:px-6 max-w-6xl">

// Full-bleed section with contained content
<section className="w-full bg-background">
  <div className="container mx-auto px-4 md:px-6">
    {/* Content */}
  </div>
</section>

// Sticky header
<header className="sticky top-0 z-50 border-b bg-background/80 backdrop-blur-sm">
```

### shadcn/ui Usage

Import from `@/components/ui/`. Never modify the base primitives. Compose instead.

```tsx
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"

// ✅ Compose shadcn components
function FeatureCard({ title, description, icon }: FeatureCardProps) {
  return (
    <Card className="border-border/50 bg-card/50 backdrop-blur-sm">
      <CardHeader>
        <div className="mb-2 text-primary">{icon}</div>
        <CardTitle className="text-lg">{title}</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-sm text-muted-foreground">{description}</p>
      </CardContent>
    </Card>
  )
}
```

### Metadata & SEO

```tsx
// app/layout.tsx
import type { Metadata } from "next"

export const metadata: Metadata = {
  title: {
    default: "Site Name",
    template: "%s | Site Name",
  },
  description: "Site description",
  openGraph: {
    type: "website",
    locale: "en_US",
    url: "https://example.com",
    siteName: "Site Name",
  },
}

// Per-page metadata
export const metadata: Metadata = {
  title: "About",  // Renders as "About | Site Name"
  description: "About page description",
}
```

### Image Handling

```tsx
import Image from "next/image"

// ✅ Always use next/image for optimization
<Image
  src="/hero.png"
  alt="Descriptive alt text"
  width={1200}
  height={630}
  priority  // Above-the-fold images
  className="rounded-lg"
/>

// ✅ For backgrounds, use CSS
<div className="bg-[url('/pattern.svg')] bg-cover bg-center">
```

### Animation

Use Tailwind's built-in animations or `tailwindcss-animate` (included with shadcn). For complex animations, use Framer Motion.

```tsx
// ✅ Tailwind animate (simple)
<div className="animate-in fade-in slide-in-from-bottom-4 duration-500">

// ✅ Framer Motion (complex)
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5, delay: 0.1 }}
>
```

## Anti-patterns

- ❌ CSS modules or styled-components alongside Tailwind
- ❌ `"use client"` on components that don't need interactivity
- ❌ Inline styles (`style={{}}`) — use Tailwind classes
- ❌ Modifying shadcn/ui base primitives in `components/ui/`
- ❌ `useEffect` for data fetching — use Server Components or React Query
- ❌ Hardcoded colors — use CSS variables (`text-foreground`, `bg-background`)
- ❌ Desktop-first responsive (`lg:` then overriding down)
- ❌ `<img>` tags — use `next/image`
- ❌ `<a>` tags for internal links — use `next/link`

## Gotchas

1. **Tailwind v4 uses CSS-first config.** No `tailwind.config.ts` by default. Theme in `app/globals.css` via `@theme`.
2. **shadcn/ui uses CSS variables for theming.** Colors defined in `globals.css` as HSL values under `:root` and `.dark`.
3. **`cn()` helper is essential.** Always use `cn()` from `lib/utils` for conditional classes: `cn("base-classes", condition && "conditional-class")`.
4. **Server Components can't use hooks.** If you need state, extract the interactive part into a separate client component.
5. **Fonts load in layout.tsx.** Use `next/font/google` or `next/font/local`. Apply via CSS variable, not className on every element.
6. **Images in `public/` are static.** For dynamic images, use external URLs with `next.config.ts` `images.remotePatterns`.
