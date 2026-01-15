---
name: sensei:design-component
description: Design a frontend component with distinctive aesthetics using sensei-designer
argument-hint: "<component-type> [--context <description>]"
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Task
  - AskUserQuestion
---

<objective>
Design a frontend component that avoids generic AI aesthetics.

Uses the sensei-designer agent to create distinctive, human-quality component designs with full specifications and implementation code.
</objective>

<context>
Component type: $ARGUMENTS

Common types: button, card, form, input, modal, navigation, hero, footer, sidebar, dropdown, tabs, accordion, toast, badge, avatar, table
</context>

<process>

<step name="gather">
**Gather design context:**

Check for existing design system:
- Look for CSS variables in stylesheets
- Check for design tokens
- Find existing component patterns

If design system exists:
- Load color tokens
- Load typography scale
- Load spacing system

If no design system:
- Offer to create foundations first
- Or proceed with sensible defaults
</step>

<step name="clarify">
**Clarify component requirements:**

Ask user (if not provided in --context):
1. What's the primary purpose?
2. Where will it appear?
3. What personality? (bold/subtle, playful/serious)
4. Any specific constraints?
</step>

<step name="design">
**Spawn sensei-designer for component creation:**

```
Task(
  prompt="Design a {component_type} component.

Mode: component-design

Context:
- Purpose: {purpose}
- Location: {where it appears}
- Personality: {personality}
- Constraints: {constraints}

Existing design system:
{tokens if found, or 'None - create sensible defaults'}

Requirements:
1. Must NOT look generic or AI-generated
2. All states: default, hover, focus, active, disabled
3. Responsive behavior defined
4. Implementation-ready CSS and HTML/JSX

Verify against anti-pattern checklist before completing.",
  subagent_type="sensei-designer"
)
```
</step>

<step name="output">
**Present design and implementation:**

Show:
- Design intent and personality
- Visual specifications
- All states
- Responsive behavior
- Implementation code

Offer:
- Save to file
- Adjust specifications
- Design related components
</step>

</process>

<success_criteria>
- [ ] Component designed with distinctive aesthetics
- [ ] No anti-patterns present
- [ ] All states defined
- [ ] Implementation code provided
- [ ] Ready to use or iterate
</success_criteria>
