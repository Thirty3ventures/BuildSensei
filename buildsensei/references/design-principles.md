<design-principles-reference>

<purpose>
Core design principles and best practices for frontend development in 2025-2026.
This reference guides the sensei-designer agent and all design-related commands.
</purpose>

<core_principles>

## 1. Distinctiveness Over Generic Patterns

- **Avoid template-like designs**: Generic layouts lack personality
- **Brand differentiation**: Unconventional layouts, custom animations, bold typography
- **Asymmetrical compositions**: Break grid monotony with deliberate asymmetry
- **Personalization**: Design decisions feel intentional, not copy-pasted

**Implementation:**
- Study brand voice and translate to visual hierarchy
- Use custom color palettes, not default design system colors
- Create motion that tells a story
- Push whitespace distribution beyond conventional margins

## 2. User-Centric Functionality

- Every visual element must serve a purpose
- Form follows function—design supports user goals
- Interactive feedback is immediate and meaningful
- Cognitive load minimized through clear information architecture

## 3. Accessibility as Foundation

- WCAG 2.2 compliance is mandatory, not optional
- Accessible design is inherently better design
- Build accessibility from the start—retrofitting is inefficient
- Test with real assistive technologies

## 4. Performance is a Design Feature

- Slow sites are poor design, regardless of aesthetics
- Every animation, image, interaction should justify its cost
- Progressive enhancement ensures core functionality without JavaScript
- Mobile-first approach with responsive considerations

## 5. Type-Safe & Maintainable

- TypeScript is the standard; untyped code is a liability
- Code organization reflects design thinking
- Components are self-documenting through clear APIs
- Future developers should understand intent without excessive comments

</core_principles>

<visual_trends>

## Visual Design Trends 2025-2026

### Trend 1: Micro-Interactions & Intentional Motion
- Hover states: 150-250ms transitions
- Loading: skeleton screens, smooth progress
- Cursor: custom cursors, responsive morphing
- Transitions guide attention and reinforce hierarchy
- Animations have purpose: guide, confirm, delight—never distract

### Trend 2: Bento Grid & Modular Layouts
- CSS Grid with auto-fit/auto-fill for responsive behavior
- Vary element sizes for visual interest and hierarchy
- Mix content types within grid cells
- Asymmetry creates dynamism

```css
.bento-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 16px;
  grid-auto-rows: auto;
}
```

### Trend 3: Layering & Dimensionality
- Shadows convey depth and elevation
- Layered cards create visual interest
- Textures add tactile quality
- Glassmorphism adds sophistication when restrained

### Trend 4: 3D Elements & Interactive Experiences
- Three.js, Babylon.js for immersive 3D
- Use for hero sections, product showcases, data viz
- Mobile needs simplified 3D or fallbacks
- 3D should feel playful, not forced

### Trend 5: Scroll-Triggered Animations
- Scroll reveals content progressively
- Parallax adds depth without excessive computation
- Choreographed sequences tell stories
- Respect `prefers-reduced-motion`
- Performance budgets ensure smooth 60fps

### Trend 6: Bold, Vibrant Color Palettes
- Rich, saturated colors (not pastels)
- Multi-directional gradients with texture
- Intentional emotional impact
- Maintain 4.5:1 contrast ratios
- Dark mode: rethink, don't just invert

### Trend 7: Massive, Expressive Typography
- Hero sections use typography as anchors (48px+)
- Variable fonts for fluid, responsive typography
- Type animation adds engagement
- Intentional font pairing

```css
h1 {
  font-size: clamp(2rem, 5vw, 4rem);
  letter-spacing: -0.02em;
  line-height: 1.1;
  font-weight: 600;
}
```

### Trend 8: Deconstructed Hero Sections
- Break away from symmetrical layouts
- Mix text, image, interactive elements asymmetrically
- Strategic negative space
- First impression matters—make it bold

### Trend 9: Morphism & Tactile Design
- Textures add personality without overwhelming
- Shadows follow consistent light direction
- Depth is suggested, not heavy-handed

### Trend 10: Dark Mode as Standard
- Design simultaneously for light and dark
- Use semantic color tokens that adapt
- Subtle shade variations prevent flatness

```css
:root {
  --color-background: #ffffff;
  --color-text: #1a1a1a;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-background: #1a1a1a;
    --color-text: #ffffff;
  }
}
```

</visual_trends>

<framework_practices>

## Framework-Specific Best Practices

### React (2025-2026)

**Core Principles:**
- Functional components are standard
- Hooks simplify state and side-effects
- TypeScript is mandatory
- Server components handle data fetching
- Concurrent features enable non-blocking renders

**State Management:**
1. Local: useState, useReducer
2. Context: app-wide, infrequently changing (Theme, User)
3. Server: React Query or SWR
4. URL: useSearchParams for filters
5. Global: Zustand only if truly needed

**Component Template:**
```typescript
import React, { useState, useCallback } from 'react';
import { cn } from '@/utils/cn';
import type { ComponentProps } from 'react';

interface Props extends ComponentProps<'div'> {
  variant?: 'default' | 'outlined';
}

export const Component: React.FC<Props> = ({
  variant = 'default',
  className,
  children,
  ...props
}) => {
  return (
    <div
      className={cn(
        'base-styles',
        variant === 'outlined' && 'border',
        className
      )}
      {...props}
    >
      {children}
    </div>
  );
};
```

### Vue 3 (2025-2026)

**Core Principles:**
- Composition API is standard
- TypeScript with `<script setup lang="ts">`
- Proxy-based reactivity
- Single-file components
- Vite as build tool

**Component Template:**
```vue
<template>
  <div :class="{ card: true, 'is-active': isActive }">
    <slot />
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

interface Props {
  variant?: 'default' | 'outlined';
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'default'
});

const isActive = ref(false);
</script>

<style scoped>
.card {
  @apply p-4 rounded-lg;
}
</style>
```

### Svelte 5 (2025-2026)

**Why Svelte:**
- No virtual DOM = faster performance
- Compile-time optimization = smaller bundles
- Built-in reactivity without hooks
- SvelteKit for full-stack

**Component Template:**
```svelte
<script lang="ts">
  interface Props {
    variant?: 'default' | 'outlined';
  }

  let { variant = 'default', children }: Props = $props();
  let isActive = $state(false);
</script>

<div
  class="card"
  class:is-active={isActive}
  onclick={() => isActive = !isActive}
>
  {@render children()}
</div>

<style>
  .card {
    padding: 1rem;
    border-radius: 0.5rem;
  }
</style>
```

</framework_practices>

<accessibility_requirements>

## Accessibility Implementation

### WCAG 2.2 Standards (POUR)

1. **Perceivable**: Users can see/hear content
2. **Operable**: Users can navigate with keyboard
3. **Understandable**: Content is clear and predictable
4. **Robust**: Works with assistive technology

### Critical Implementation

**Semantic HTML:**
```html
<header>  <!-- Navigation, logo -->
<nav>     <!-- Primary navigation -->
<main>    <!-- Main content -->
<article> <!-- Self-contained content -->
<section> <!-- Thematic grouping -->
<aside>   <!-- Sidebar, related -->
<footer>  <!-- Site-wide info -->
```

**Form Accessibility:**
```html
<label for="email">Email Address</label>
<input
  id="email"
  type="email"
  required
  aria-invalid={hasError}
  aria-describedby="email-error"
/>
<p id="email-error" role="alert">{errorMessage}</p>
```

**Keyboard Navigation:**
- All interactive elements focusable
- Logical focus order
- No keyboard traps
- Focus trapped in modals

**Focus Indicators:**
```css
.button:focus-visible {
  outline: 3px solid #0066ff;
  outline-offset: 2px;
}
```

**Motion Accessibility:**
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation: none !important;
    transition: none !important;
  }
}
```

**Common ARIA:**
- `aria-label`: Invisible label for icon buttons
- `aria-labelledby`: Links to element ID
- `aria-describedby`: Links to description
- `aria-live`: Announces dynamic content
- `aria-expanded`: Disclosure state
- `aria-hidden="true"`: Hide decorative elements

</accessibility_requirements>

<performance_standards>

## Performance Optimization

### Core Web Vitals Targets

| Metric | Target | Strategy |
|--------|--------|----------|
| LCP | < 2.5s | Prioritize critical content, lazy-load below-fold |
| FID | < 100ms | Code-split, defer non-critical scripts |
| CLS | < 0.1 | Reserve space for images/ads, font-display swap |

### Image Optimization
- Use Next.js Image or equivalent
- Modern formats (WebP with fallback)
- Responsive sizes attribute
- Lazy loading below-fold

### Font Loading
```css
@font-face {
  font-family: 'CustomFont';
  src: url('font.woff2') format('woff2');
  font-display: swap;
}
```

### JavaScript Optimization
- Code-splitting at route level
- Dynamic imports for heavy components
- Tree-shaking (ES modules)
- Defer non-critical scripts

### React Optimization
```typescript
// Memoization
const Component = React.memo(({ id }) => <div>{id}</div>);

// useMemo for expensive calculations
const sorted = useMemo(() => items.sort(), [items]);

// useCallback for stable references
const handleClick = useCallback(() => onClick(id), [id, onClick]);

// Virtual scrolling for long lists
import { FixedSizeList } from 'react-window';
```

### Bundle Targets
- Initial JS: < 100KB
- Total JS: < 300KB
- CSS: < 50KB

</performance_standards>

<component_architecture>

## Component Architecture

### Atomic Design Methodology

```
Atoms (Basic building blocks)
├── Button, Input, Label, Icon

Molecules (Simple groups)
├── FormGroup, SearchBar, Card

Organisms (Complex sections)
├── Header, UserForm, DataTable

Templates (Page layouts)
├── DashboardLayout, AuthLayout

Pages (Specific instances)
├── UsersDashboard, LoginPage
```

### Component Best Practices

**Single Responsibility:**
```typescript
// Separate concerns
export const useUser = (userId) => { /* fetch */ };
export const formatUserData = (user) => { /* format */ };
export const UserCard = ({ user }) => { /* display */ };
```

**Composition Over Props:**
```typescript
// Prefer composition
<Button variant="primary" size="lg">
  <Icon name="plus" />
  <Button.Text>Add Item</Button.Text>
</Button>

// Over prop explosion
<Button icon={<Icon />} iconPosition="left" ... />
```

**Forward Refs:**
```typescript
export const Input = forwardRef<HTMLInputElement, InputProps>(
  (props, ref) => <input ref={ref} {...props} />
);
```

</component_architecture>

<design_tokens>

## Design Token Structure

```typescript
export const tokens = {
  colors: {
    primary: {
      50: '#f0f7ff',
      100: '#e0f0ff',
      500: '#0066ff',
      600: '#0052cc',
      900: '#003d99',
    },
    neutral: {
      50: '#f9fafb',
      100: '#f3f4f6',
      500: '#6b7280',
      900: '#111827',
    },
  },
  typography: {
    fontFamily: {
      sans: ['Inter', 'system-ui', 'sans-serif'],
      mono: ['JetBrains Mono', 'monospace'],
    },
    fontSize: {
      xs: '0.75rem',
      sm: '0.875rem',
      base: '1rem',
      lg: '1.125rem',
      xl: '1.25rem',
      '2xl': '1.5rem',
      '3xl': '1.875rem',
      '4xl': '2.25rem',
    },
  },
  spacing: {
    1: '0.25rem',
    2: '0.5rem',
    3: '0.75rem',
    4: '1rem',
    6: '1.5rem',
    8: '2rem',
    12: '3rem',
    16: '4rem',
  },
  radius: {
    sm: '0.25rem',
    md: '0.5rem',
    lg: '0.75rem',
    xl: '1rem',
    full: '9999px',
  },
  shadows: {
    sm: '0 1px 2px rgba(0, 0, 0, 0.05)',
    md: '0 4px 6px rgba(0, 0, 0, 0.1)',
    lg: '0 10px 15px rgba(0, 0, 0, 0.1)',
    xl: '0 20px 25px rgba(0, 0, 0, 0.15)',
  },
  transitions: {
    fast: '150ms cubic-bezier(0.16, 1, 0.3, 1)',
    normal: '250ms cubic-bezier(0.16, 1, 0.3, 1)',
    slow: '350ms cubic-bezier(0.16, 1, 0.3, 1)',
  },
};
```

</design_tokens>

<quality_standards>

## Code Quality Standards

### TypeScript Configuration
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### ESLint Configuration
```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/strict",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended",
    "prettier"
  ]
}
```

### Testing Strategy
1. **Unit**: Individual functions, components
2. **Integration**: Component interactions
3. **E2E**: Full user journeys (Playwright)
4. **Visual**: Snapshot regression
5. **Accessibility**: jest-axe automated checks

</quality_standards>

<decision_summary>

## Design Agent Decision Tree

```
User Request
    ↓
What is the primary goal?
    ↓
┌──────────────────────────────────────────────┐
│  Visual Impact  │  Forms  │  Data Display    │
└──────────────────────────────────────────────┘
    ↓              ↓              ↓
Bold colors    Clear labels   Bento grids
Animations     Error states   Virtual scroll
3D elements    Touch-friendly Pagination
    │              │              │
    └──────────────┼──────────────┘
                   ↓
Framework Selection
    ↓
┌──────────────────────────────────────────────┐
│  Performance Critical?  → Svelte 5           │
│  Heavy Interactivity?   → React + Next.js    │
│  Simpler State Model?   → Vue 3 + Nuxt       │
└──────────────────────────────────────────────┘
    ↓
Implementation Checklist
    ↓
┌──────────────────────────────────────────────┐
│ ✓ TypeScript strict mode                     │
│ ✓ WCAG 2.2 compliance                        │
│ ✓ Responsive design                          │
│ ✓ Performance budgets                        │
│ ✓ Dark mode support                          │
│ ✓ Error boundaries                           │
│ ✓ Loading states                             │
└──────────────────────────────────────────────┘
```

**Build with intention. Ship with confidence.**

</decision_summary>

</design-principles-reference>
