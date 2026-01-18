<design-planning-workflow>

<purpose>
Workflow for creating detailed design implementation plans based on captured design context.
Called by /sensei:plan-design command.
</purpose>

<when_to_use>
- After /sensei:discuss-design has captured requirements
- When design context exists and planning is needed
- Before executing design implementation
</when_to_use>

<required_reading>
@references/design-principles.md - Design trends and best practices
@templates/spec/component.md - Component specification format
</required_reading>

<inputs>
- .planning/design/DESIGN-CONTEXT.md (required)
- package.json (for existing project detection)
- Existing codebase structure
</inputs>

<decision_frameworks>

<framework name="framework_selection">
## Framework Selection Decision Matrix

### Scoring Criteria (1-5 scale)

| Factor | Weight | React | Vue | Svelte |
|--------|--------|-------|-----|--------|
| Performance (bundle, runtime) | High if perf critical | 3 | 4 | 5 |
| Ecosystem (libraries, tools) | High if complex app | 5 | 4 | 3 |
| Learning curve | High if tight timeline | 3 | 4 | 5 |
| Enterprise adoption | High if corporate | 5 | 3 | 2 |
| SSR/SSG support | High if SEO matters | 5 | 5 | 5 |
| Animation libraries | High if essential | 5 | 3 | 4 |
| State management | High if complex state | 5 | 4 | 4 |
| TypeScript support | Always high | 5 | 5 | 5 |
| Job market/hiring | For teams | 5 | 4 | 2 |

### Decision Rules

**Choose React/Next.js when:**
- Large team or enterprise context
- Need Framer Motion for complex animations
- Extensive third-party integrations
- Complex state management (multiple stores)
- Existing React expertise

**Choose Vue/Nuxt when:**
- Simpler mental model preferred
- Template-based development fits team
- Progressive enhancement important
- Smaller learning curve needed
- Clean separation of concerns valued

**Choose Svelte/SvelteKit when:**
- Performance is paramount
- Bundle size critical (mobile, low bandwidth)
- Less boilerplate desired
- Simpler reactivity model wanted
- Smaller project scope

**Default recommendation if no preference:**
- Marketing/content site → Svelte (performance)
- Enterprise app → React (ecosystem)
- Rapid prototyping → Vue (simplicity)
</framework>

<framework name="styling_selection">
## Styling Strategy Decision

### Based on Design System Choice

| Design System | Recommended Styling | Rationale |
|---------------|---------------------|-----------|
| shadcn/ui | Tailwind CSS | Built for Tailwind, CSS variables |
| Material UI | Emotion/sx prop | Native MUI approach |
| Chakra UI | Style props | Chakra's design API |
| Ant Design | Less/CSS Modules | Ant's theming system |
| Custom | Tailwind CSS | Most flexible, utility-first |
| None | Tailwind CSS | Best DX for custom designs |

### Tailwind Configuration

If Tailwind selected:

```javascript
// tailwind.config.js structure
module.exports = {
  content: ['./src/**/*.{js,ts,jsx,tsx}'],
  darkMode: 'class', // or 'media'
  theme: {
    extend: {
      colors: {
        // From design tokens
        primary: {
          50: 'var(--color-primary-50)',
          // ...
          900: 'var(--color-primary-900)',
        },
        // Semantic colors
        background: 'var(--color-background)',
        foreground: 'var(--color-foreground)',
      },
      fontFamily: {
        sans: ['var(--font-sans)', 'system-ui', 'sans-serif'],
        mono: ['var(--font-mono)', 'monospace'],
      },
      spacing: {
        // Custom spacing if needed
      },
      borderRadius: {
        // From tokens
      },
      boxShadow: {
        // From tokens
      },
      animation: {
        // Custom animations
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
};
```

### CSS-in-JS Configuration (if selected)

```typescript
// theme.ts for Emotion/styled-components
export const theme = {
  colors: {
    primary: { /* token values */ },
    neutral: { /* token values */ },
  },
  typography: {
    fonts: { /* font stacks */ },
    sizes: { /* size scale */ },
  },
  spacing: { /* spacing scale */ },
  shadows: { /* shadow scale */ },
  radii: { /* border radius scale */ },
  transitions: { /* transition presets */ },
};
```
</framework>

<framework name="state_management">
## State Management Decision

### State Categories

| Category | Description | Recommended Solution |
|----------|-------------|---------------------|
| UI State | Dropdowns, modals, hover | Local component state |
| Form State | Input values, validation | react-hook-form / vee-validate |
| Server State | API data, cache | TanStack Query / SWR |
| Global UI | Theme, notifications | Context / Pinia / Stores |
| URL State | Filters, pagination | URL params (built-in) |
| Auth State | User session | Context + server state |

### React State Strategy

```
Complexity Level → Solution

Simple:
  Local: useState
  Forms: useState or react-hook-form
  Server: fetch + useState
  Global: Context API

Moderate:
  Local: useState, useReducer
  Forms: react-hook-form
  Server: TanStack Query
  Global: Context API (split by domain)

Complex:
  Local: useState, useReducer
  Forms: react-hook-form + zod
  Server: TanStack Query
  Global: Zustand (if Context insufficient)
  Real-time: TanStack Query + WebSocket
```

### Vue State Strategy

```
Complexity Level → Solution

Simple:
  Local: ref, reactive
  Forms: v-model
  Server: fetch + ref
  Global: provide/inject

Moderate:
  Local: ref, reactive, computed
  Forms: VeeValidate + zod
  Server: VueQuery / useFetch (Nuxt)
  Global: Pinia

Complex:
  Local: composables
  Forms: VeeValidate + zod
  Server: VueQuery
  Global: Pinia (multiple stores)
```

### Svelte State Strategy

```
Complexity Level → Solution

Simple:
  Local: $state, $derived
  Forms: bind:value
  Server: fetch + $state
  Global: Svelte stores

Moderate:
  Local: $state, $derived, $effect
  Forms: superforms
  Server: SvelteKit load functions
  Global: Svelte stores

Complex:
  Local: runes
  Forms: superforms + zod
  Server: load + streaming
  Global: custom stores
```
</framework>

<framework name="component_architecture">
## Component Architecture Planning

### Atomic Design Structure

```
Level 1: Atoms (primitives)
├── Button
├── Input
├── Select
├── Checkbox
├── Radio
├── Switch
├── Icon
├── Badge
├── Avatar
├── Spinner
├── Skeleton
└── Divider

Level 2: Molecules (combinations)
├── FormField (Label + Input + Error)
├── SearchBar (Input + Button + Icon)
├── Card (Container + Content areas)
├── ListItem (Avatar + Text + Actions)
├── MenuItem (Icon + Text + Shortcut)
├── Toast (Icon + Message + Close)
├── Alert (Icon + Content + Actions)
└── Tooltip (Trigger + Content)

Level 3: Organisms (sections)
├── Header (Logo + Nav + Actions)
├── Sidebar (Nav groups + User)
├── DataTable (Headers + Rows + Pagination)
├── Form (Multiple FormFields + Actions)
├── Modal (Header + Content + Footer)
├── CommandPalette (Search + Results)
└── UserMenu (Avatar + Dropdown)

Level 4: Templates (layouts)
├── DashboardLayout
├── AuthLayout
├── MarketingLayout
├── SettingsLayout
└── DocumentLayout

Level 5: Pages (instances)
├── Specific page implementations
└── Feature-specific compositions
```

### Component Estimation

Based on scope:

| Scope | Atoms | Molecules | Organisms | Total |
|-------|-------|-----------|-----------|-------|
| Single component | 0-2 | 0-1 | 0-1 | 1-4 |
| Component library | 10-15 | 8-12 | 5-8 | 23-35 |
| Feature | 5-8 | 4-6 | 2-4 | 11-18 |
| Full app | 12-18 | 10-15 | 8-12 | 30-45 |

### Component Dependencies

```
Button ← Card, Modal, Alert, Form, Table
Input ← FormField, SearchBar, Form
Icon ← Button, MenuItem, Alert, Toast
Avatar ← UserMenu, ListItem, Comment
Card ← Dashboard, List, Grid layouts
Modal ← Dialogs, Confirmations, Forms
Table ← Data pages, Admin panels
```
</framework>

<framework name="animation_planning">
## Animation Strategy Planning

### Based on Animation Level

**Essential (Rich Experience):**
```
Library: Framer Motion (React) / built-in (Svelte/Vue)

Animations to implement:
- Page transitions (route changes)
- Component mount/unmount (fade, scale, slide)
- Scroll-triggered reveals (intersection observer)
- Micro-interactions (hover, focus, active)
- Loading orchestration (staggered skeleton → content)
- Gesture support (drag, swipe, pinch)
- Layout animations (list reordering)
- Shared element transitions
- Custom cursor effects
- Parallax scrolling

Performance considerations:
- Use GPU-accelerated properties (transform, opacity)
- Implement will-change hints
- Virtual scrolling for long lists
- Defer off-screen animations
- Respect prefers-reduced-motion
```

**Moderate (Functional Feedback):**
```
Library: CSS transitions + minimal JS

Animations to implement:
- Hover states (150-200ms)
- Focus indicators
- Modal/dropdown enter/exit (200-250ms)
- Button feedback (press effect)
- Loading skeletons
- Toast enter/exit
- Collapse/expand

Performance considerations:
- CSS-only where possible
- Single property transitions
- Avoid layout triggers
```

**Minimal (Performance Priority):**
```
Approach: CSS transitions only

Animations to implement:
- Focus outlines
- Hover color changes
- Opacity transitions for show/hide

Performance considerations:
- No animation libraries
- Instant transitions where possible
- Skeleton loaders instead of spinners
```

### Animation Tokens

```typescript
// animations.ts
export const animations = {
  duration: {
    instant: '0ms',
    fast: '150ms',
    normal: '250ms',
    slow: '350ms',
    slower: '500ms',
  },
  easing: {
    default: 'cubic-bezier(0.16, 1, 0.3, 1)', // ease-out-expo
    bounce: 'cubic-bezier(0.34, 1.56, 0.64, 1)',
    smooth: 'cubic-bezier(0.4, 0, 0.2, 1)',
    spring: 'cubic-bezier(0.175, 0.885, 0.32, 1.275)',
  },
};
```
</framework>

<framework name="accessibility_planning">
## Accessibility Implementation Planning

### WCAG 2.2 AA Requirements

```
Perceivable:
[ ] 1.1.1 Non-text content has text alternatives
[ ] 1.3.1 Information and relationships programmatically determinable
[ ] 1.4.1 Color is not the only visual means of conveying information
[ ] 1.4.3 Contrast ratio at least 4.5:1 (3:1 for large text)
[ ] 1.4.11 Non-text contrast at least 3:1
[ ] 1.4.12 Text spacing adjustable without loss of content

Operable:
[ ] 2.1.1 All functionality available from keyboard
[ ] 2.1.2 No keyboard traps
[ ] 2.4.3 Focus order logical and meaningful
[ ] 2.4.6 Headings and labels descriptive
[ ] 2.4.7 Focus visible
[ ] 2.5.3 Label in name (accessible name contains visible label)

Understandable:
[ ] 3.2.1 No unexpected context changes on focus
[ ] 3.2.2 No unexpected context changes on input
[ ] 3.3.1 Error identification
[ ] 3.3.2 Labels or instructions for user input

Robust:
[ ] 4.1.1 Parsing (valid HTML)
[ ] 4.1.2 Name, role, value for UI components
```

### Per-Component Accessibility Checklist

```markdown
## Component: {name}

### Keyboard
- [ ] Focusable with Tab
- [ ] Activatable with Enter/Space
- [ ] Escapable (if overlay)
- [ ] Arrow key navigation (if applicable)

### Screen Reader
- [ ] Accessible name (aria-label or visible label)
- [ ] Role announced (native element or ARIA role)
- [ ] State announced (expanded, selected, checked)
- [ ] Live region for dynamic content

### Visual
- [ ] Focus indicator visible
- [ ] Color contrast passes
- [ ] Not color-only indication
- [ ] Works with zoom (200%)

### Motion
- [ ] Respects prefers-reduced-motion
- [ ] No flashing content
```
</framework>

</decision_frameworks>

<output_structure>
## DESIGN-PLAN.md Structure

See /sensei:plan-design command for full template.

Key sections:
1. Executive Summary
2. Framework Selection (with rationale)
3. Component Architecture
4. Styling Strategy
5. State Management
6. Animation Plan
7. Accessibility Plan
8. Performance Targets
9. Implementation Roadmap
</output_structure>

</design-planning-workflow>
