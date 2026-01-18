---
name: sensei:execute-design
description: Execute design plan using sensei-designer agent
argument-hint: "[component name or 'all']"
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash, Task]
---

<objective>
Execute the design implementation plan using the sensei-designer agent.

This command:
1. Reads DESIGN-PLAN.md for implementation details
2. Spawns sensei-designer agent with full context
3. Implements components following design principles
4. Generates component specifications
5. Creates tests and documentation

Can execute:
- All planned components (default or 'all')
- Specific component by name
- Component category (ui, forms, navigation, etc.)
</objective>

<execution_context>
@~/.claude/buildsensei/references/design-principles.md
@~/.claude/buildsensei/templates/spec/component.md
</execution_context>

<context>
$ARGUMENTS - Optional: specific component, category, or 'all'

Required:
@.planning/design/DESIGN-PLAN.md
@.planning/design/DESIGN-CONTEXT.md

Project context:
@package.json
@tsconfig.json (if TypeScript)
@tailwind.config.js (if Tailwind)
</context>

<process>

<step name="validate">
## Validate Prerequisites

```bash
# Check for design plan
if [ ! -f .planning/design/DESIGN-PLAN.md ]; then
  echo "ERROR: No design plan found."
  echo "Run /sensei:discuss-design and /sensei:plan-design first."
  exit 1
fi

cat .planning/design/DESIGN-PLAN.md
cat .planning/design/DESIGN-CONTEXT.md
```

Check project setup:
```bash
# Verify framework is installed
cat package.json | grep -E "(react|vue|svelte)"

# Check for required directories
ls -la src/ components/ app/ 2>/dev/null
```

If project not set up:
- Offer to run setup commands from DESIGN-PLAN.md
- Create directory structure
</step>

<step name="determine_scope">
## Determine Execution Scope

Parse $ARGUMENTS:

**If empty or 'all':**
- Execute full implementation roadmap from DESIGN-PLAN.md
- Follow phase order: Foundation → Core → Complex → Features → Polish

**If specific component name:**
- Execute only that component
- Example: `/sensei:execute-design Button`

**If category:**
- Execute all components in that category
- Example: `/sensei:execute-design ui` (all ui components)
- Example: `/sensei:execute-design forms` (all form components)

Build execution task list based on scope.
</step>

<step name="setup_foundation" condition="scope includes foundation">
## Setup Foundation (if not exists)

Check if foundation is already set up:
```bash
ls src/styles/tokens.* 2>/dev/null
ls src/utils/cn.* 2>/dev/null
```

If not set up:

### 1. Create Design Tokens
Based on DESIGN-PLAN.md tokens specification, create:
- `src/styles/tokens.ts` - Design token definitions
- `src/styles/globals.css` - Global styles and CSS variables

### 2. Create Utility Functions
- `src/utils/cn.ts` - Class name merging (if Tailwind)
- `src/utils/variants.ts` - Component variant helpers

### 3. Setup Theme Provider (if needed)
Based on framework:
- React: Theme context provider
- Vue: Provide/inject theme
- Svelte: Theme store

### 4. Configure Tailwind (if applicable)
Update `tailwind.config.js` with design tokens.

Commit foundation:
```bash
git add src/styles/ src/utils/
git commit -m "feat(design): Setup design foundation

- Design tokens configured
- Utility functions created
- Theme system initialized

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>"
```
</step>

<step name="execute_components">
## Execute Component Implementation

For each component in scope, spawn sensei-designer agent:

```
Use Task tool with subagent_type: sensei-designer

Prompt for agent:
---
## Component Implementation Task

**Component:** {component_name}
**Category:** {category}

### Context Files
Read these for full context:
- .planning/design/DESIGN-CONTEXT.md - Design requirements
- .planning/design/DESIGN-PLAN.md - Implementation plan

### Design Principles
Follow the design principles in your knowledge:
- Distinctiveness over generic patterns
- Accessibility as foundation
- Performance as feature
- Type-safe and maintainable

### Framework: {framework}
### Styling: {styling_approach}
### Design System: {design_system}

### Component Requirements

**Purpose:** {component_purpose}
**Variants:** {variants_needed}
**Props:** {expected_props}

### Implementation Checklist

1. [ ] Create component file with TypeScript
2. [ ] Define props interface
3. [ ] Implement all variants
4. [ ] Add accessibility attributes (ARIA, keyboard)
5. [ ] Support dark mode
6. [ ] Add loading/error states (if applicable)
7. [ ] Create basic tests
8. [ ] Generate component spec

### Output Files
- src/components/{category}/{ComponentName}.tsx
- src/components/{category}/{ComponentName}.test.tsx
- docs/spec/components/{category}/{ComponentName}.spec.md

### Commit Format
feat(design): Add {ComponentName} component

- {feature_1}
- {feature_2}
- Accessible: keyboard nav, ARIA labels
- Dark mode supported

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>
---
```

### Execution Order

**Phase 1: UI Primitives (Wave 1 - Parallel)**
- Button
- Input
- Select
- Checkbox
- Icon
- Badge

**Phase 2: Layout (Wave 2 - Parallel)**
- Container
- Stack
- Grid
- Divider
- Spacer

**Phase 3: Data Display (Wave 3 - Parallel)**
- Card
- Avatar
- List
- Table (if needed)

**Phase 4: Feedback (Wave 4 - After data display)**
- Alert
- Toast
- Modal
- Skeleton
- Spinner

**Phase 5: Navigation (Wave 5 - Parallel)**
- Navbar
- Sidebar
- Tabs
- Breadcrumb

**Phase 6: Forms (Wave 6 - After UI primitives)**
- FormField
- SearchBar
- DatePicker (if needed)
- FileUpload (if needed)

**Phase 7: Features (Wave 7 - After all base components)**
- Project-specific components

For each wave:
1. Spawn parallel agents for independent components
2. Wait for wave completion
3. Verify all components work together
4. Move to next wave
</step>

<step name="verify_components">
## Verify Implemented Components

After each component:

### 1. Type Check
```bash
npx tsc --noEmit
```

### 2. Lint Check
```bash
npm run lint 2>/dev/null || npx eslint src/components/
```

### 3. Test Run
```bash
npm test -- --watchAll=false --passWithNoTests
```

### 4. Accessibility Check
```bash
# If Storybook with a11y addon
npm run storybook:test 2>/dev/null
```

### 5. Visual Verification
List created components and their features.
</step>

<step name="generate_specs">
## Generate Component Specifications

For each implemented component, generate spec:

Use `/sensei:generate-specs --components` or inline generation:

```markdown
# {ComponentName}

> {description}

**Location:** `src/components/{category}/{ComponentName}.tsx`
**Category:** {category}

## Props

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
{props_table}

## Variants

| Variant | Description |
|---------|-------------|
{variants_table}

## Examples

### Basic Usage
```tsx
{basic_example}
```

### With Variants
```tsx
{variant_example}
```

## Accessibility

- Keyboard: {keyboard_behavior}
- Screen Reader: {sr_behavior}
- ARIA: {aria_attributes}

---
*Generated by BuildSensei sensei-designer*
```

Save to `docs/spec/components/{category}/{ComponentName}.spec.md`
</step>

<step name="update_index">
## Update Component Exports

Create or update index files:

```typescript
// src/components/ui/index.ts
export { Button } from './Button';
export { Input } from './Input';
export { Select } from './Select';
// ...

// src/components/index.ts
export * from './ui';
export * from './layout';
export * from './forms';
export * from './data-display';
export * from './feedback';
export * from './navigation';
```
</step>

<step name="final_commit">
## Final Commit

If multiple components were created:

```bash
git add src/components/ docs/spec/components/
git commit -m "feat(design): Implement {scope} components

Components created:
{component_list}

All components include:
- TypeScript definitions
- Accessibility support
- Dark mode support
- Component specifications

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>"
```
</step>

<step name="summary">
## Execution Summary

```markdown
## Design Execution Complete

### Components Implemented

| Component | Category | Status | Spec |
|-----------|----------|--------|------|
{component_status_table}

### Files Created

**Components:**
{component_files}

**Specifications:**
{spec_files}

### Quality Checks

| Check | Status |
|-------|--------|
| TypeScript | {pass/fail} |
| Linting | {pass/fail} |
| Tests | {pass/fail} |
| Accessibility | {pass/fail} |

### Next Steps

1. **Review components** in browser/Storybook
2. **Run full test suite:** `npm test`
3. **Check accessibility:** Use axe DevTools
4. **Integrate** into application

---

**To add more components:**
`/sensei:execute-design {ComponentName}`

**To regenerate specs:**
`/sensei:generate-specs --components`
```
</step>

</process>

<parallel_execution>
## Parallel Execution Strategy

When executing multiple components:

1. **Group by dependency:**
   - Wave 1: Components with no dependencies (Button, Icon)
   - Wave 2: Components that depend on Wave 1 (Card uses Button)
   - etc.

2. **Spawn parallel agents:**
   - Use Task tool with multiple invocations
   - Max 3 concurrent agents (configurable)
   - Each agent handles one component

3. **Synchronization:**
   - Wait for wave completion before next wave
   - Verify inter-component compatibility
   - Resolve any conflicts

Example:
```
Wave 1: Button + Input + Icon (parallel)
         ↓ (all complete)
Wave 2: Card + FormField + Badge (parallel)
         ↓ (all complete)
Wave 3: Modal + Alert (parallel)
```
</parallel_execution>

<success_criteria>
- [ ] Design plan validated
- [ ] Foundation set up (tokens, utils, theme)
- [ ] Components implemented per scope
- [ ] All components type-checked
- [ ] Accessibility attributes added
- [ ] Component specs generated
- [ ] Tests created (basic)
- [ ] All changes committed
- [ ] Summary provided
</success_criteria>
