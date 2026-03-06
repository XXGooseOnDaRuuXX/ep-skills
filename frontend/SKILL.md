---
name: frontend
description: Next.js 16 + App Router + shadcn/ui + Tailwind v4 patterns for production web apps
when_to_use: When building any web application with the Next.js + shadcn + Tailwind stack
---

# Frontend — Next.js 16 + shadcn/ui + Tailwind v4

## Quick Reference

**Stack:** Next.js 16.1+ (App Router) / React 19.2 / TypeScript 5+ / Tailwind v4 / shadcn/ui
**Build tool:** Turbopack (default in Next.js 16, no `--turbopack` flag needed)
**Package manager:** pnpm
**Node.js:** 20.9+ minimum (Node 18 dropped)
**Styling:** Tailwind utility classes + CSS variables for theming. No CSS modules, no styled-components.
**Components:** shadcn/ui as the base. Extend, don't replace.

## Next.js 16 Key Changes

### Turbopack Is Default
No more `--turbopack` flag. `next dev` and `next build` use Turbopack automatically.

```json
// ✅ Next.js 16 — clean scripts
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

Turbopack config is now top-level (not under `experimental`):
```ts
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  turbopack: {
    // options (no longer experimental.turbopack)
  },
}
export default nextConfig
```

If you need Webpack for production: `next build --webpack`.

### Async Request APIs (Breaking)
`cookies()`, `headers()`, `draftMode()`, `params`, and `searchParams` are ALL async now. Synchronous access is fully removed.

```tsx
// ✅ Next.js 16 — async params
export default async function Page(props: PageProps<'/blog/[slug]'>) {
  const { slug } = await props.params
  const query = await props.searchParams
  return <h1>Blog Post: {slug}</h1>
}

// ❌ Next.js 15 style (BROKEN in 16)
export default function Page({ params }: { params: { slug: string } }) {
  return <h1>{params.slug}</h1>
}
```

Use `npx next typegen` to auto-generate `PageProps`, `LayoutProps`, `RouteContext` type helpers.

### Cache Components (opt-in, major feature)
Mix static, cached, and dynamic content in a single route. Enable in config:

```ts
// next.config.ts
const nextConfig: NextConfig = {
  cacheComponents: true,
}
```

Use `use cache` directive for cacheable components:
```tsx
"use cache"

export async function ProductReviews({ productId }: { productId: string }) {
  const reviews = await fetchReviews(productId)
  return <ReviewList reviews={reviews} />
}
```

Wrap dynamic content in `<Suspense>`:
```tsx
import { Suspense } from 'react'

export default function ProductPage() {
  return (
    <div>
      <ProductInfo />           {/* Static shell */}
      <Suspense fallback={<CartSkeleton />}>
        <Cart />                {/* Dynamic, deferred to request time */}
      </Suspense>
    </div>
  )
}
```

### React 19.2 Features
- **View Transitions:** Native browser-powered route transition animations via `<ViewTransition>`
- **`useEffectEvent()`:** Stable event handlers that don't re-trigger effects
- **`<Activity>`:** Component for managing offscreen content lifecycle
- **`refresh()`:** Granular cache refresh from client: `refresh(['notifications', 'unread-count'])`

### Proxy (replaces middleware convention)
The `middleware` convention is deprecated in favor of `proxy`:
```ts
// proxy.ts (replaces middleware.ts)
export function proxy(request: Request) {
  // routing, auth checks, redirects
}
```

### Next.js DevTools MCP
Next.js 16 ships with an MCP server for AI coding assistants:
```json
// .mcp.json
{
  "mcpServers": {
    "next-devtools": {
      "command": "npx",
      "args": ["-y", "next-devtools-mcp@latest"]
    }
  }
}
```

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

// Sticky header with backdrop blur
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
```

### Animation

Use Tailwind's built-in animations or `tailwindcss-animate` (included with shadcn). For route transitions, use React 19.2 View Transitions. For complex animations, use Framer Motion.

```tsx
// ✅ React 19.2 View Transitions (route animations)
import { ViewTransition } from 'react'
<ViewTransition>
  <Link href="/about">About</Link>
</ViewTransition>

// ✅ Tailwind animate (simple)
<div className="animate-in fade-in slide-in-from-bottom-4 duration-500">

// ✅ Framer Motion (complex)
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5, delay: 0.1 }}
>
```

### Tailwind v4 Theming

Tailwind v4 uses CSS-first config. Theme defined in `app/globals.css` via `@theme`:

```css
@import "tailwindcss";

@theme {
  --color-background: #0a0a0a;
  --color-foreground: #ededed;
  --color-primary: #3b82f6;
  --font-sans: "Geist", sans-serif;
  --font-mono: "Geist Mono", monospace;
}
```

No `tailwind.config.ts` needed for basic theming.

## Anti-patterns

- ❌ CSS modules or styled-components alongside Tailwind
- ❌ `"use client"` on components that don't need interactivity
- ❌ Synchronous `params` or `searchParams` access (broken in Next.js 16)
- ❌ `tailwind.config.ts` for basic theming (use CSS `@theme` in v4)
- ❌ `experimental.turbopack` in config (moved to top-level `turbopack`)
- ❌ Inline styles (`style={{}}`) — use Tailwind classes
- ❌ Modifying shadcn/ui base primitives in `components/ui/`
- ❌ `useEffect` for data fetching — use Server Components or `use cache`
- ❌ Hardcoded colors — use CSS variables (`text-foreground`, `bg-background`)
- ❌ Desktop-first responsive (`lg:` then overriding down)
- ❌ `<img>` tags — use `next/image`
- ❌ `<a>` tags for internal links — use `next/link`
- ❌ `--turbopack` flag in scripts (default in Next.js 16)

## Gotchas

1. **Turbopack is default.** If you have a custom `webpack` config, the build will FAIL. Use `--webpack` flag to opt out, or migrate to Turbopack config.
2. **All request APIs are async.** `cookies()`, `headers()`, `params`, `searchParams` must be awaited. Run `npx next typegen` for type helpers.
3. **Cache Components are opt-in.** Set `cacheComponents: true` in next.config.ts. Without it, you get the old rendering model.
4. **Tailwind v4 CSS-first config.** Theme in `globals.css` via `@theme`, not in `tailwind.config.ts`.
5. **shadcn/ui uses CSS variables for theming.** Colors defined in `globals.css` as HSL values under `:root` and `.dark`.
6. **`cn()` helper is essential.** Always use `cn()` from `lib/utils` for conditional classes.
7. **Server Components can't use hooks.** Extract interactive parts into separate client components.
8. **Fonts load in layout.tsx.** Use `next/font/google` or `next/font/local`. Apply via CSS variable.
9. **`middleware.ts` is deprecated.** Use `proxy.ts` for routing, auth checks, redirects.
10. **Next.js DevTools MCP is available.** Add `.mcp.json` for AI-assisted development and upgrades.
