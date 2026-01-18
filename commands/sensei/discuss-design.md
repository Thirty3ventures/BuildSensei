---
name: sensei:discuss-design
description: Guided discovery for frontend design decisions
argument-hint: "[component or feature name]"
allowed-tools: [Read, Write, Glob, Grep, AskUserQuestion]
---

<objective>
Walk through a guided discovery process to understand design requirements before planning.

This command captures:
- What you're building and why
- Who the users are
- Brand and aesthetic direction
- Technical constraints and requirements
- Accessibility needs

Output: `.planning/design/DESIGN-CONTEXT.md`

Use this before `/sensei:plan-design` to ensure the design approach matches your vision.
</objective>

<execution_context>
@~/.claude/buildsensei/workflows/design-discovery.md
@~/.claude/buildsensei/references/design-principles.md
</execution_context>

<context>
$ARGUMENTS - Optional component/feature name to focus discussion

Existing context (if available):
@.planning/PROJECT.md
@.planning/design/DESIGN-CONTEXT.md (if exists - for updates)
@CLAUDE.md (for project context)
</context>

<process>

<step name="initialize">
## Initialize Design Context

```bash
mkdir -p .planning/design
```

Check if DESIGN-CONTEXT.md exists:
- If exists, ask if updating or starting fresh
- If not, start new discovery
</step>

<step name="understand_scope">
## Understand What You're Building

Use AskUserQuestion:

**Question 1: What are you designing?**
- header: "Design Scope"
- question: "What are you looking to design?"
- options:
  - "Full application UI" - Complete application interface
  - "Feature/Section" - Specific feature like dashboard, settings, etc.
  - "Component library" - Reusable component system
  - "Single component" - One specific component

**Question 2: Primary interaction type**
- header: "Interaction"
- question: "What's the primary user interaction?"
- options:
  - "Visual/Marketing" - Landing pages, showcases, portfolios
  - "Data-heavy" - Dashboards, tables, analytics
  - "Forms/Input" - Data entry, workflows, wizards
  - "Content/Reading" - Blogs, documentation, articles

Capture responses for context file.
</step>

<step name="understand_users">
## Understand Your Users

Use AskUserQuestion:

**Question 3: Who are your users?**
- header: "Users"
- question: "Who will use this interface?"
- options:
  - "General consumers" - Broad public audience
  - "Business/Enterprise" - Professional users, B2B
  - "Developers/Technical" - Technical audience
  - "Internal team" - Company employees only

**Question 4: Device priority**
- header: "Devices"
- question: "What devices are most important?"
- options:
  - "Mobile-first" - Primarily mobile users
  - "Desktop-first" - Primarily desktop users
  - "Equal priority" - Both equally important
  - "Specific device" - Tablet, kiosk, TV, etc.

Capture responses.
</step>

<step name="understand_brand">
## Understand Brand & Aesthetics

Use AskUserQuestion:

**Question 5: Design personality**
- header: "Personality"
- question: "What personality should the design convey?"
- options:
  - "Bold & Vibrant" - Strong colors, dramatic, attention-grabbing
  - "Clean & Minimal" - Lots of whitespace, simple, focused
  - "Playful & Fun" - Rounded, colorful, animated
  - "Professional & Serious" - Corporate, trustworthy, structured

**Question 6: Visual trends preference**
- header: "Style"
- question: "Which visual style appeals to you?"
- multiSelect: true
- options:
  - "Bento grids" - Modern card-based layouts with varied sizes
  - "Glassmorphism" - Frosted glass, blur effects, transparency
  - "Bold typography" - Large, expressive text as design element
  - "3D/Depth" - Layering, shadows, dimensional elements

**Question 7: Color direction**
- header: "Colors"
- question: "Do you have color preferences?"
- options:
  - "Use existing brand colors" - I have a color palette
  - "Suggest modern palette" - Create something fresh
  - "Dark mode priority" - Design for dark backgrounds
  - "High contrast" - Accessibility-focused colors

Capture responses.
</step>

<step name="understand_technical">
## Understand Technical Context

Use AskUserQuestion:

**Question 8: Existing design system?**
- header: "Design System"
- question: "Are you using an existing component library?"
- options:
  - "shadcn/ui" - Radix-based, Tailwind styled
  - "Material UI" - Google's Material Design
  - "Chakra UI" - Accessible, customizable
  - "None/Custom" - Building from scratch or other

**Question 9: Framework preference**
- header: "Framework"
- question: "Do you have a framework preference, or should I recommend?"
- options:
  - "React/Next.js" - I'm using or want React
  - "Vue/Nuxt" - I'm using or want Vue
  - "Svelte/SvelteKit" - I'm using or want Svelte
  - "Recommend for me" - Help me choose based on needs

**Question 10: Animation requirements**
- header: "Animation"
- question: "How important are animations?"
- options:
  - "Essential" - Rich animations are key to the experience
  - "Moderate" - Some micro-interactions and transitions
  - "Minimal" - Just functional transitions
  - "Performance-first" - Avoid animations for speed

Capture responses.
</step>

<step name="understand_constraints">
## Understand Constraints

Use AskUserQuestion:

**Question 11: Accessibility requirements**
- header: "Accessibility"
- question: "What accessibility level do you need?"
- options:
  - "WCAG 2.2 AA (Recommended)" - Standard compliance
  - "WCAG 2.2 AAA" - Highest accessibility standard
  - "Basic" - Keyboard navigation and screen readers
  - "Not sure" - Help me understand requirements

**Question 12: Performance constraints**
- header: "Performance"
- question: "Any specific performance requirements?"
- multiSelect: true
- options:
  - "Core Web Vitals priority" - Google ranking signals
  - "Offline support needed" - PWA/Service workers
  - "Real-time updates" - Live data, WebSockets
  - "Standard performance" - Normal web app expectations

Capture responses.
</step>

<step name="capture_vision">
## Capture Additional Vision

If user provided $ARGUMENTS (specific component/feature):
- Ask clarifying questions about that specific element
- What problem does it solve?
- Any examples or inspiration?

Otherwise, ask:

**Open question:**
"Is there anything else about your design vision I should know? Any inspiration sites, specific requirements, or constraints?"

Allow free-form response.
</step>

<step name="generate_context">
## Generate DESIGN-CONTEXT.md

Create comprehensive design context file:

```markdown
# Design Context

<!--
  Generated by BuildSensei /sensei:discuss-design
  Created: {timestamp}
  Last updated: {timestamp}
-->

## Project Overview

**Scope:** {full_app | feature | component_library | single_component}
**Focus:** {component_name if provided}
**Primary Interaction:** {visual | data | forms | content}

## Target Users

**Audience:** {consumers | enterprise | developers | internal}
**Device Priority:** {mobile | desktop | equal | specific}
**Technical Sophistication:** {inferred from audience}

## Brand & Aesthetics

### Personality
{bold | clean | playful | professional}

### Visual Direction
{selected styles as bullets}

### Color Strategy
{brand_colors | modern_palette | dark_mode | high_contrast}

**Color Notes:**
{any specific color preferences or constraints}

## Technical Context

### Design System
**Base:** {shadcn | mui | chakra | custom}
**Customization Level:** {minimal | moderate | heavy}

### Framework
**Selected/Preferred:** {react | vue | svelte | recommend}
**Rationale:** {if recommend, will be filled by plan-design}

### Animation Strategy
**Level:** {essential | moderate | minimal | performance}
**Approach:** {to be defined in plan-design}

## Constraints

### Accessibility
**Target Level:** {aa | aaa | basic}
**Key Requirements:**
- Keyboard navigation
- Screen reader support
- Color contrast compliance
- Reduced motion support

### Performance
**Priorities:**
{selected performance requirements}

**Budgets:**
- LCP: < 2.5s
- FID: < 100ms
- CLS: < 0.1

## Additional Context

{free-form vision notes}

## Inspiration & References

{any mentioned examples or inspiration}

---

## Next Steps

Run `/sensei:plan-design` to:
1. Finalize framework selection
2. Define component architecture
3. Create implementation plan

---
*Generated by BuildSensei. Ready for /sensei:plan-design*
```

Write to `.planning/design/DESIGN-CONTEXT.md`
</step>

<step name="commit">
## Commit Design Context

```bash
git add .planning/design/DESIGN-CONTEXT.md
git commit -m "docs(design): Capture design context and requirements

Scope: {scope}
Audience: {audience}
Style: {personality}
Framework: {framework_preference}

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>"
```
</step>

<step name="summary">
## Summary

```markdown
## Design Context Captured

I've documented your design requirements:

| Aspect | Decision |
|--------|----------|
| Scope | {scope} |
| Interaction | {interaction_type} |
| Users | {audience} |
| Personality | {personality} |
| Framework | {framework} |
| Accessibility | {accessibility_level} |

**Saved to:** `.planning/design/DESIGN-CONTEXT.md`

---

## Next Step

**Plan the design** — framework selection, component architecture, implementation approach

`/sensei:plan-design`
```
</step>

</process>

<success_criteria>
- [ ] User walked through all discovery questions
- [ ] Responses captured comprehensively
- [ ] DESIGN-CONTEXT.md created with full context
- [ ] Context committed to git
- [ ] User knows next step (plan-design)
</success_criteria>
