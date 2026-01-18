---
name: sensei:plan-design
description: Create frontend design plan with framework and architecture decisions
argument-hint: "[component name]"
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion]
---

<objective>
Create a comprehensive design implementation plan based on the design context.

This command:
1. Analyzes DESIGN-CONTEXT.md requirements
2. Recommends/confirms framework selection with rationale
3. Defines component architecture (atomic design)
4. Plans state management approach
5. Specifies styling strategy
6. Creates implementation roadmap

Output: `.planning/design/DESIGN-PLAN.md`

Run after `/sensei:discuss-design` and before `/sensei:execute-design`.
</objective>

<execution_context>
@~/.claude/buildsensei/workflows/design-planning.md
@~/.claude/buildsensei/references/design-principles.md
@~/.claude/buildsensei/templates/spec/component.md
</execution_context>

<context>
$ARGUMENTS - Optional specific component to plan

Required:
@.planning/design/DESIGN-CONTEXT.md

Optional:
@.planning/PROJECT.md
@package.json (for existing dependencies)
@CLAUDE.md
</context>

<process>

<step name="validate">
## Validate Prerequisites

```bash
# Check for design context
if [ ! -f .planning/design/DESIGN-CONTEXT.md ]; then
  echo "ERROR: No design context found."
  echo "Run /sensei:discuss-design first to capture requirements."
  exit 1
fi

cat .planning/design/DESIGN-CONTEXT.md
```

Also check for existing project:
```bash
cat package.json 2>/dev/null | head -30
ls src/ components/ app/ 2>/dev/null | head -20
```
</step>

<step name="analyze_context">
## Analyze Design Context

Parse DESIGN-CONTEXT.md to extract:
- Scope (full app, feature, component library, single component)
- Interaction type (visual, data, forms, content)
- User audience
- Personality and visual direction
- Framework preference
- Animation requirements
- Accessibility level
- Performance constraints

Build decision matrix for recommendations.
</step>

<step name="framework_decision">
## Framework Selection

Based on context analysis, recommend or confirm framework:

### Decision Matrix

| Factor | React/Next.js | Vue/Nuxt | Svelte/SvelteKit |
|--------|---------------|----------|------------------|
| Heavy interactivity | ★★★ | ★★☆ | ★★☆ |
| Performance critical | ★★☆ | ★★☆ | ★★★ |
| Large ecosystem | ★★★ | ★★☆ | ★☆☆ |
| Learning curve | ★★☆ | ★★★ | ★★★ |
| SSR/SSG support | ★★★ | ★★★ | ★★★ |
| Animation libraries | ★★★ | ★★☆ | ★★☆ |
| Enterprise adoption | ★★★ | ★★☆ | ★☆☆ |

### Recommendation Logic

**Choose React/Next.js when:**
- Heavy interactivity required
- Large team or enterprise context
- Need extensive ecosystem (Framer Motion, React Query, etc.)
- Complex state management needs

**Choose Vue/Nuxt when:**
- Simpler mental model preferred
- Progressive enhancement important
- Template-based development preferred
- Smaller bundle size matters

**Choose Svelte/SvelteKit when:**
- Performance is top priority
- Smaller bundle critical
- Less boilerplate desired
- Simpler reactivity model wanted

If user selected "Recommend for me":
- Use AskUserQuestion to confirm recommendation with rationale

If user had preference:
- Validate it makes sense for requirements
- Note any concerns if mismatch detected
</step>

<step name="architecture_decision">
## Component Architecture

Define structure based on atomic design:

```
/src (or /app for Next.js)
├── /components
│   ├── /ui                    # Atoms: Button, Input, Icon, Badge
│   ├── /layout                # Atoms: Container, Stack, Grid, Divider
│   ├── /forms                 # Molecules: FormField, SearchBar, Select
│   ├── /data-display          # Molecules: Card, Table, List, Avatar
│   ├── /feedback              # Molecules: Alert, Toast, Modal, Skeleton
│   ├── /navigation            # Molecules: Navbar, Sidebar, Breadcrumb
│   └── /features              # Organisms: Feature-specific components
│       └── /{feature-name}
│           ├── index.ts
│           ├── {Feature}.tsx
│           └── {Feature}.test.tsx
├── /hooks                     # Custom hooks (React) / Composables (Vue)
├── /stores                    # State management (if needed)
├── /utils                     # Helper functions
├── /types                     # TypeScript definitions
├── /styles                    # Global styles, tokens
│   ├── tokens.ts             # Design tokens
│   └── globals.css           # Global CSS
└── /lib                       # Third-party integrations
```

Adjust based on:
- Framework choice (Next.js app router vs pages, etc.)
- Scope (single component doesn't need full structure)
- Existing project structure
</step>

<step name="styling_decision">
## Styling Strategy

Based on design system choice and requirements:

### If shadcn/ui selected:
```
- Base: Tailwind CSS
- Components: Radix UI primitives
- Styling: CSS variables + Tailwind
- Theming: CSS custom properties
- Dark mode: class-based toggle
```

### If Material UI selected:
```
- Base: Emotion (CSS-in-JS)
- Components: MUI components
- Styling: sx prop + styled()
- Theming: MUI theme provider
- Dark mode: Theme palette switching
```

### If Chakra UI selected:
```
- Base: Emotion (CSS-in-JS)
- Components: Chakra components
- Styling: Style props
- Theming: Chakra theme
- Dark mode: useColorMode hook
```

### If Custom/None selected:
```
- Base: Tailwind CSS (recommended) or CSS Modules
- Components: Built from scratch
- Styling: Utility-first or component CSS
- Theming: CSS custom properties
- Dark mode: prefers-color-scheme + data attribute
```

### Design Tokens Structure
```typescript
// styles/tokens.ts
export const tokens = {
  colors: {
    primary: { 50: '...', 500: '...', 900: '...' },
    neutral: { ... },
    semantic: { success: '...', error: '...', warning: '...' }
  },
  typography: {
    fontFamily: { sans: '...', mono: '...' },
    fontSize: { xs: '...', sm: '...', base: '...', lg: '...' },
    fontWeight: { normal: 400, medium: 500, bold: 700 }
  },
  spacing: { 1: '0.25rem', 2: '0.5rem', ... },
  radius: { sm: '0.25rem', md: '0.5rem', lg: '1rem', full: '9999px' },
  shadows: { sm: '...', md: '...', lg: '...' },
  transitions: { fast: '150ms', normal: '250ms', slow: '350ms' }
};
```
</step>

<step name="state_decision">
## State Management Strategy

Based on complexity and framework:

### React State Strategy
```
1. Local State (useState, useReducer)
   → Component-specific UI state
   → Form state (with react-hook-form)

2. Context API
   → Theme state
   → Auth state
   → Notification state
   → Avoid: Large, frequently changing state

3. Server State (TanStack Query / SWR)
   → API data fetching
   → Caching and revalidation
   → Optimistic updates

4. URL State (useSearchParams)
   → Filters, pagination, sorting
   → Shareable state

5. Global State (Zustand - if needed)
   → Only for complex cross-cutting state
   → Avoid Redux unless truly necessary
```

### Vue State Strategy
```
1. Reactive refs (ref, reactive)
   → Component-local state

2. Pinia Stores
   → Shared state across components
   → Server state with caching

3. URL State (vue-router)
   → Query parameters
```

### Svelte State Strategy
```
1. Runes ($state, $derived)
   → Component state

2. Svelte Stores
   → Shared state
   → Simple pub/sub pattern

3. URL State (SvelteKit)
   → $page.url.searchParams
```
</step>

<step name="animation_decision">
## Animation Strategy

Based on animation requirements:

### Essential Animations
```
Library: Framer Motion (React) / Svelte transitions / Vue transitions
Approach:
- Page transitions (route changes)
- Component enter/exit animations
- Scroll-triggered reveals
- Micro-interactions on all interactive elements
- Loading state animations
- Gesture support (drag, swipe)

Performance:
- Use CSS transforms/opacity (GPU accelerated)
- Implement will-change hints
- Respect prefers-reduced-motion
- Virtual scrolling for large lists
```

### Moderate Animations
```
Library: CSS transitions + minimal JS
Approach:
- Hover/focus states (150-250ms)
- Modal/dropdown enter/exit
- Loading skeletons
- Button feedback

Performance:
- CSS-only where possible
- JS for orchestrated sequences only
```

### Minimal Animations
```
Approach:
- CSS transitions only
- Functional feedback (button press, focus)
- No decorative motion

Performance:
- Maximize performance
- No animation libraries
```
</step>

<step name="accessibility_plan">
## Accessibility Implementation Plan

Based on target level:

### WCAG 2.2 AA Checklist
```
[ ] Semantic HTML (header, nav, main, footer, article, section)
[ ] Keyboard navigation (Tab, Enter, Escape, Arrow keys)
[ ] Focus management (visible indicators, trap in modals)
[ ] Color contrast (4.5:1 text, 3:1 large text/UI)
[ ] Screen reader support (ARIA labels, live regions)
[ ] Form accessibility (labels, error messages, required indicators)
[ ] Skip links (skip to main content)
[ ] Reduced motion support (@media prefers-reduced-motion)
[ ] Image alt text
[ ] Link purpose clarity
```

### Testing Tools
```
- axe DevTools (Chrome extension)
- Lighthouse accessibility audit
- NVDA/VoiceOver screen reader testing
- Keyboard-only navigation testing
```

### Implementation Approach
```
1. Start with semantic HTML
2. Add ARIA only when HTML isn't sufficient
3. Test with keyboard after each component
4. Run axe check before PR merge
```
</step>

<step name="generate_plan">
## Generate DESIGN-PLAN.md

```markdown
# Design Implementation Plan

<!--
  Generated by BuildSensei /sensei:plan-design
  Created: {timestamp}
  Based on: .planning/design/DESIGN-CONTEXT.md
-->

## Executive Summary

**Scope:** {scope}
**Framework:** {framework} - {rationale}
**Design System:** {design_system}
**Timeline:** {estimated_components} components

## Framework Selection

### Decision: {React/Next.js | Vue/Nuxt | Svelte/SvelteKit}

**Rationale:**
{why this framework fits the requirements}

**Key Benefits:**
- {benefit_1}
- {benefit_2}
- {benefit_3}

**Trade-offs:**
- {tradeoff_1}

### Project Setup
```bash
{setup_commands}
```

### Key Dependencies
```json
{
  "dependencies": {
    {framework_deps}
  },
  "devDependencies": {
    {dev_deps}
  }
}
```

## Component Architecture

### Directory Structure
```
{architecture_tree}
```

### Component Categories

| Category | Purpose | Examples |
|----------|---------|----------|
| ui | Atomic primitives | Button, Input, Icon |
| layout | Structure components | Container, Grid, Stack |
| forms | Form-related | FormField, Select, Checkbox |
| data-display | Show information | Card, Table, Avatar |
| feedback | User communication | Alert, Modal, Toast |
| navigation | Movement | Navbar, Sidebar, Tabs |
| features | Business logic | {project-specific} |

### Component Checklist

For each component, ensure:
- [ ] TypeScript props interface
- [ ] Default variants defined
- [ ] Keyboard accessible
- [ ] ARIA attributes where needed
- [ ] Responsive behavior
- [ ] Dark mode support
- [ ] Loading/error states (where applicable)

## Styling Strategy

### Approach: {tailwind | css-in-js | css-modules}

### Design Tokens

{tokens_structure}

### Theming

**Light/Dark Mode:**
{theming_approach}

**Brand Customization:**
{customization_approach}

## State Management

### Strategy

{state_strategy}

### State Categories

| Type | Solution | Use Cases |
|------|----------|-----------|
| Local UI | {local_solution} | Dropdowns, modals, hover |
| Form | {form_solution} | Input values, validation |
| Server | {server_solution} | API data, caching |
| Global | {global_solution} | Theme, auth, notifications |
| URL | {url_solution} | Filters, pagination |

## Animation Plan

### Level: {essential | moderate | minimal}

### Approach

{animation_approach}

### Key Animations

| Element | Animation | Duration |
|---------|-----------|----------|
| Buttons | Scale + shadow on hover | 150ms |
| Modals | Fade + scale enter/exit | 250ms |
| Page transitions | Fade between routes | 300ms |
| Loading | Skeleton shimmer | continuous |

### Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

## Accessibility Plan

### Target: WCAG 2.2 {level}

### Implementation Checklist

{accessibility_checklist}

### Testing Protocol

1. **During development:** Keyboard test each component
2. **Before PR:** Run axe DevTools
3. **Weekly:** Screen reader spot checks
4. **Pre-release:** Full WCAG audit

## Performance Targets

### Core Web Vitals

| Metric | Target | Strategy |
|--------|--------|----------|
| LCP | < 2.5s | Optimize images, critical CSS |
| FID | < 100ms | Code splitting, defer non-critical |
| CLS | < 0.1 | Reserve space, font-display swap |

### Bundle Size Targets

| Bundle | Target |
|--------|--------|
| Initial JS | < 100KB |
| Total JS | < 300KB |
| CSS | < 50KB |

## Implementation Roadmap

### Phase 1: Foundation
- [ ] Project setup with {framework}
- [ ] Design tokens configuration
- [ ] Base component structure
- [ ] Theme system (light/dark)

### Phase 2: Core Components
- [ ] Button, Input, Select (ui)
- [ ] Container, Stack, Grid (layout)
- [ ] Card, Avatar (data-display)

### Phase 3: Complex Components
- [ ] Modal, Toast, Alert (feedback)
- [ ] Navbar, Sidebar (navigation)
- [ ] Table, List (data-display)
- [ ] Form components (forms)

### Phase 4: Feature Components
- [ ] {feature-specific components}

### Phase 5: Polish
- [ ] Animation implementation
- [ ] Accessibility audit
- [ ] Performance optimization
- [ ] Documentation

---

## Next Step

**Execute the design plan:**

`/sensei:execute-design`

This will use the sensei-designer agent to implement components following this plan.

---
*Generated by BuildSensei. Ready for /sensei:execute-design*
```

Write to `.planning/design/DESIGN-PLAN.md`
</step>

<step name="commit">
## Commit Design Plan

```bash
git add .planning/design/DESIGN-PLAN.md
git commit -m "docs(design): Create design implementation plan

Framework: {framework}
Design System: {design_system}
Components: {component_count} planned
Accessibility: WCAG 2.2 {level}

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>"
```
</step>

<step name="summary">
## Summary

```markdown
## Design Plan Created

### Key Decisions

| Aspect | Decision |
|--------|----------|
| Framework | {framework} |
| Styling | {styling_approach} |
| State | {state_approach} |
| Animations | {animation_level} |
| Accessibility | WCAG 2.2 {level} |

### Implementation Roadmap

1. **Foundation** - Setup, tokens, theme
2. **Core Components** - Button, Input, Card, etc.
3. **Complex Components** - Modal, Table, Nav
4. **Features** - Project-specific components
5. **Polish** - Animations, a11y, performance

**Saved to:** `.planning/design/DESIGN-PLAN.md`

---

## Next Step

**Execute the plan** — build components with sensei-designer

`/sensei:execute-design`
```
</step>

</process>

<success_criteria>
- [ ] DESIGN-CONTEXT.md analyzed
- [ ] Framework selected with rationale
- [ ] Architecture defined
- [ ] Styling strategy determined
- [ ] State management planned
- [ ] Animation approach set
- [ ] Accessibility plan created
- [ ] DESIGN-PLAN.md generated
- [ ] Plan committed to git
- [ ] User knows next step (execute-design)
</success_criteria>
