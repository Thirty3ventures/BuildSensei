<design-discovery-workflow>

<purpose>
Detailed workflow for discovering and capturing design requirements through guided questioning.
Called by /sensei:discuss-design command.
</purpose>

<when_to_use>
- Starting a new frontend design project
- Adding significant UI features to existing project
- Creating a component library
- Designing a single complex component
</when_to_use>

<required_reading>
@references/design-principles.md - Core design philosophy and trends
</required_reading>

<discovery_areas>

<area name="scope">
## Scope Discovery

**Goal:** Understand what's being designed and its boundaries.

### Questions to Ask

1. **What are you designing?**
   - Full application UI
   - Specific feature/section (dashboard, settings, onboarding)
   - Component library
   - Single component

2. **What's the primary purpose?**
   - Marketing/conversion (landing pages)
   - Data visualization (dashboards, analytics)
   - Data entry (forms, workflows)
   - Content consumption (blogs, docs)
   - Communication (chat, messaging)
   - E-commerce (product listings, checkout)

3. **What's the scope boundary?**
   - What's explicitly included?
   - What's explicitly excluded?
   - Any existing designs to work with?

### Signals to Capture
- Project complexity level
- Number of unique screens/views
- Interaction density (simple → complex)
</area>

<area name="users">
## User Discovery

**Goal:** Understand who will use the interface and how.

### Questions to Ask

1. **Who are the primary users?**
   - Demographics (age, technical ability)
   - Context (work, personal, mobile)
   - Frequency of use (daily, occasional)

2. **What devices do they use?**
   - Mobile-first (phones primarily)
   - Desktop-first (work tools)
   - Cross-device (responsive priority)
   - Specific (kiosk, TV, tablet)

3. **What's their technical sophistication?**
   - Power users (shortcuts, advanced features)
   - Casual users (guided, simple)
   - Mixed audience

4. **Any accessibility needs?**
   - Known user accessibility requirements
   - Regulatory requirements (ADA, Section 508)
   - General best practice

### Signals to Capture
- Primary device viewport
- Interaction patterns (touch vs mouse)
- Patience level (fast task completion vs exploration)
</area>

<area name="brand">
## Brand & Aesthetic Discovery

**Goal:** Understand the visual direction and personality.

### Questions to Ask

1. **What personality should it convey?**
   - Bold & Vibrant - Strong, attention-grabbing
   - Clean & Minimal - Focused, no distraction
   - Playful & Fun - Engaging, delightful
   - Professional & Serious - Trustworthy, corporate
   - Luxury & Premium - Refined, exclusive
   - Technical & Developer - Code-like, precise

2. **Any existing brand guidelines?**
   - Logo and brand colors
   - Typography requirements
   - Tone of voice

3. **Visual style preferences?**
   - Bento grids (modern card layouts)
   - Glassmorphism (blur, transparency)
   - Bold typography (text as design element)
   - 3D elements (depth, dimension)
   - Illustrations (custom graphics)
   - Photography (real images)
   - Gradients (color transitions)
   - Dark mode preference

4. **Any inspiration or examples?**
   - Sites they admire
   - Competitors to differentiate from
   - Specific elements they like

5. **Color direction?**
   - Existing brand palette
   - Color psychology needs (trust=blue, energy=orange)
   - Light/dark mode requirements

### Signals to Capture
- Primary brand color (if any)
- Visual complexity preference
- Animation tolerance
</area>

<area name="technical">
## Technical Context Discovery

**Goal:** Understand technical constraints and preferences.

### Questions to Ask

1. **Existing tech stack?**
   - Current framework (React, Vue, Svelte, none)
   - Build tools (Vite, Next.js, Nuxt, SvelteKit)
   - Styling approach (Tailwind, CSS modules, styled-components)

2. **Design system in use?**
   - shadcn/ui (Radix + Tailwind)
   - Material UI
   - Chakra UI
   - Ant Design
   - Custom/None

3. **Framework preference (if greenfield)?**
   - React/Next.js (enterprise, ecosystem)
   - Vue/Nuxt (simplicity, progressive)
   - Svelte/SvelteKit (performance, minimal)
   - No preference (recommend based on needs)

4. **Animation requirements?**
   - Essential (rich, immersive experience)
   - Moderate (functional transitions)
   - Minimal (performance priority)
   - None (static, fast)

5. **Performance constraints?**
   - Core Web Vitals critical (SEO, Google ranking)
   - Offline support needed (PWA)
   - Real-time updates (WebSocket, live data)
   - Low bandwidth users

6. **Integration requirements?**
   - APIs to integrate
   - Authentication system
   - Analytics/tracking
   - Third-party widgets

### Signals to Capture
- Framework choice (or need to recommend)
- Animation budget
- Performance priority level
</area>

<area name="constraints">
## Constraints Discovery

**Goal:** Understand limitations and requirements.

### Questions to Ask

1. **Accessibility requirements?**
   - WCAG 2.2 AA (standard, recommended)
   - WCAG 2.2 AAA (highest standard)
   - Basic (keyboard + screen reader)
   - Regulatory compliance needed

2. **Browser support?**
   - Modern only (last 2 versions)
   - Legacy support (IE11, old Safari)
   - Specific browser requirements

3. **Internationalization?**
   - Single language
   - RTL language support needed
   - Multiple languages planned

4. **Content constraints?**
   - Fixed content (known text)
   - Dynamic content (CMS, user-generated)
   - Variable length handling

5. **Timeline/budget implications?**
   - Speed priority (ship fast)
   - Quality priority (polish matters)
   - Balanced

### Signals to Capture
- Accessibility target level
- Browser support matrix
- i18n requirements
</area>

</discovery_areas>

<output_format>
## DESIGN-CONTEXT.md Structure

```markdown
# Design Context

<!--
  Generated by BuildSensei /sensei:discuss-design
  Created: {ISO_timestamp}
  Last updated: {ISO_timestamp}
-->

## Project Overview

**Scope:** {full_app | feature:{name} | component_library | single_component:{name}}
**Primary Purpose:** {marketing | data_viz | data_entry | content | communication | ecommerce}
**Complexity:** {simple | moderate | complex}

### Boundaries
**Included:**
- {included_item_1}
- {included_item_2}

**Excluded:**
- {excluded_item_1}

## Target Users

**Primary Audience:** {description}
**Technical Level:** {power_user | casual | mixed}
**Device Priority:** {mobile_first | desktop_first | responsive}

### User Needs
- {need_1}
- {need_2}

### Accessibility Requirements
**Target:** WCAG 2.2 {AA | AAA | Basic}
**Special Considerations:** {any_specific_needs}

## Brand & Aesthetics

### Personality
**Primary:** {bold | clean | playful | professional | luxury | technical}
**Secondary:** {optional_secondary_trait}

### Visual Direction
{list of selected visual styles}

### Colors
**Approach:** {brand_colors | modern_palette | dark_priority | high_contrast}
**Primary Color:** {color_if_known}
**Notes:** {any_color_constraints}

### Inspiration
{any mentioned examples or references}

## Technical Context

### Stack
**Framework:** {react | vue | svelte | recommend}
**Meta-framework:** {nextjs | nuxt | sveltekit | none}
**Styling:** {tailwind | css_modules | css_in_js | plain_css}

### Design System
**Base:** {shadcn | mui | chakra | ant | custom | none}
**Customization:** {minimal | moderate | heavy}

### Animation
**Level:** {essential | moderate | minimal | none}
**Key Areas:** {where_animation_matters}

### Performance
**Priority:** {critical | high | normal}
**Requirements:**
- {perf_requirement_1}
- {perf_requirement_2}

## Constraints

### Browser Support
{browser_requirements}

### Internationalization
{i18n_requirements}

### Timeline
**Priority:** {speed | quality | balanced}

## Additional Context

{free_form_notes}

---

## Discovery Summary

| Aspect | Decision |
|--------|----------|
| Scope | {scope} |
| Users | {users} |
| Personality | {personality} |
| Framework | {framework} |
| Styling | {styling} |
| Accessibility | {a11y} |
| Performance | {performance} |

---
*Ready for /sensei:plan-design*
```
</output_format>

<questioning_strategy>
## Adaptive Questioning

1. **Start broad, narrow based on answers**
   - If "full app" → ask about all areas
   - If "single component" → skip scope, focus on technical

2. **Skip redundant questions**
   - If existing project detected → skip framework choice
   - If brand colors provided → skip color direction

3. **Probe deeper when needed**
   - Vague answers → ask for examples
   - Complex requirements → break into sub-questions

4. **Validate understanding**
   - Summarize after each section
   - Allow corrections before moving on

5. **Respect user's time**
   - Essential questions only
   - Offer "skip with defaults" for optional areas
</questioning_strategy>

</design-discovery-workflow>
