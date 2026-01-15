---
name: sensei:design-review
description: Review frontend code for generic patterns and quality issues using sensei-designer
argument-hint: "[file-or-directory]"
allowed-tools:
  - Read
  - Glob
  - Grep
  - Task
  - AskUserQuestion
---

<objective>
Review frontend code for anti-patterns, generic aesthetics, and quality issues.

Uses the sensei-designer agent to audit CSS, components, and layouts for "AI slop" patterns and provide actionable fixes.
</objective>

<context>
Target: $ARGUMENTS (file path or directory to review)

If no argument provided, auto-detect frontend files:
- Look for src/components/, src/styles/, app/, pages/
- Find .css, .scss, .tsx, .jsx, .vue, .svelte files
</context>

<process>

<step name="discover">
**Find frontend files to review:**

If argument is a file:
- Review that specific file

If argument is a directory:
- Find all style files: `*.css`, `*.scss`, `*.less`
- Find all component files: `*.tsx`, `*.jsx`, `*.vue`, `*.svelte`
- Limit to 10 most recently modified

If no argument:
- Search common frontend directories
- Prioritize recently modified files
</step>

<step name="review">
**Spawn sensei-designer for review:**

```
Task(
  prompt="Review this frontend code for anti-patterns and quality issues.

Mode: design-review

Files to review:
{list of files}

For each file, check:
1. Anti-patterns (gradients, over-rounded corners, shadows everywhere)
2. Typography (hierarchy, scale, line heights)
3. Spacing (consistency, system usage)
4. Colors (palette size, contrast)
5. Layout (cookie-cutter patterns)
6. Component states (all states designed?)

Produce a consolidated review with prioritized fixes.",
  subagent_type="sensei-designer"
)
```
</step>

<step name="present">
**Present review results:**

Show:
- Overall score
- Critical issues (must fix)
- Important issues (should fix)
- Quick wins (easy improvements)
- Priority action list

Offer:
- Fix specific issues now
- Create design system first
- Review different files
</step>

</process>

<success_criteria>
- [ ] Frontend files identified
- [ ] Design review completed
- [ ] Issues categorized by severity
- [ ] Actionable fixes provided
- [ ] User knows next steps
</success_criteria>
