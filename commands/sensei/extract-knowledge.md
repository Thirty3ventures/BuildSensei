---
name: sensei:extract-knowledge
description: Extract knowledge from completed phase and persist to context files
argument-hint: "[phase-number]"
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

<objective>
Extract lessons learned, decisions, patterns, and gotchas from a completed phase
and persist them to project context files (CLAUDE.md, skills.md, references.md).

This command is called automatically after each phase execution, but can also be
run manually to extract knowledge from any completed phase.
</objective>

<execution_context>
@~/.claude/buildsensei/workflows/extract-knowledge.md
@~/.claude/buildsensei/templates/context-files/knowledge-entry.md
@~/.claude/buildsensei/templates/context-files/claude-md.md
@~/.claude/buildsensei/templates/context-files/skills-md.md
@~/.claude/buildsensei/templates/context-files/references-md.md
</execution_context>

<context>
$ARGUMENTS - Optional phase number. If not provided, extracts from most recent phase.

Read current state:
```bash
cat .planning/STATE.md 2>/dev/null
```

Find completed phases (SUMMARY files use pattern XX-YY-SUMMARY.md):
```bash
ls -la .planning/phases/*/*-SUMMARY.md 2>/dev/null
```
</context>

<process>

<step name="identify_phase">
## Identify Target Phase

1. If $ARGUMENTS contains a phase number, use that phase
2. Otherwise, read STATE.md to find most recently completed phase
3. Verify phase has SUMMARY.md files (indicating completion)
4. If no completed phases found, exit with message
</step>

<step name="gather_sources">
## Gather Knowledge Sources

For the target phase, read:

1. **All SUMMARY.md files:**
   ```bash
   cat .planning/phases/{phase}/*-SUMMARY.md
   ```

2. **VERIFICATION.md (if exists):**
   ```bash
   cat .planning/phases/{phase}/*-VERIFICATION.md 2>/dev/null
   ```

3. **Phase CONTEXT.md (if exists):**
   ```bash
   cat .planning/phases/{phase}/*-CONTEXT.md 2>/dev/null
   ```

4. **List of modified files (from SUMMARY frontmatter):**
   Extract `files_modified` from each SUMMARY.md
</step>

<step name="extract_knowledge">
## Extract Knowledge Categories

Parse the gathered sources and categorize:

### Decisions
Look for:
- Explicit "Decision:" mentions in SUMMARY
- "Chose X over Y" patterns
- Technical choices documented

Format: `| {decision} | {choice} | {rationale} | Phase {N} |`

### Gotchas
Look for:
- "Deviation:" sections in SUMMARY
- "Fixed:" or "Workaround:" mentions
- Issues encountered and resolved
- Things that didn't work as expected

Format: `- {gotcha description} (Phase {N})`

### Patterns
Look for:
- Reusable code mentioned
- "Created utility/helper/hook for..."
- Patterns used across multiple files
- Abstractions introduced

Format: Skill entry if substantial, otherwise note

### Key Files
From `files_modified` frontmatter:
- File path
- Purpose (infer from SUMMARY task description)
- Phase added

Format: `| {file} | {purpose} | Phase {N} |`

### Dependencies
Look for:
- `npm install` or `pip install` commands
- New imports from external packages
- API integrations added

Format: `- {dependency}: {why needed}`

### Technical Notes
Look for:
- Important implementation details
- Performance considerations
- Security notes
- Edge cases handled
</step>

<step name="update_claude_md">
## Update CLAUDE.md

1. Check if CLAUDE.md exists in project root
2. If not, create skeleton using template
3. Read current CLAUDE.md content
4. For each knowledge category:
   - Find the appropriate section
   - Append new entries (never overwrite existing)
   - Preserve user edits (check for USER_EDITED markers)

### Sections to Update:
- **Key Decisions table:** Append new rows
- **Gotchas Discovered:** Append new bullets
- **Patterns Discovered:** Append new bullets
- **Key Files table:** Append new rows, update if file already listed
- **Current Sprint Context:** Replace with current state
- **Session Continuity:** Update with latest position

5. Update metadata:
   - `Last updated: {timestamp}`
   - `Current phase: {phase}`
   - `phases_incorporated: [add {phase}]`
</step>

<step name="update_skills_md">
## Update skills.md (if patterns found)

If substantial reusable patterns were discovered:

1. Check if skills.md exists, create if not
2. For each pattern that qualifies as a skill:
   - Check if similar skill already exists
   - If new, add skill entry with:
     - Category
     - Location
     - Usage instructions
     - Code example
     - Phase added

Qualification criteria for skills:
- Used in 2+ places OR explicitly designed for reuse
- Self-contained (minimal dependencies)
- Has clear usage pattern
</step>

<step name="update_references_md">
## Update references.md (if new references found)

If new APIs, docs, or resources were encountered:

1. Check if references.md exists, create if not
2. For each new reference:
   - Categorize (Documentation, API, Tool, etc.)
   - Check for duplicates
   - Add to appropriate section

Sources for references:
- New package.json dependencies → link to docs
- API integrations mentioned → link to API docs
- URLs in code comments
- External services configured
</step>

<step name="update_spec_md">
## Update spec.md (feature status)

1. Check if spec.md exists
2. If exists, update feature statuses:
   - Find features associated with this phase
   - Mark as "Complete" if phase verified
   - Check off acceptance criteria that were met
</step>

<step name="backup_and_commit">
## Backup and Commit

1. Create backup of updated files:
   ```bash
   mkdir -p .planning/backups
   cp CLAUDE.md .planning/backups/CLAUDE.md.{timestamp} 2>/dev/null
   ```

2. Commit context file updates:
   ```bash
   git add CLAUDE.md spec.md skills.md references.md 2>/dev/null
   git commit -m "docs(context): Extract knowledge from Phase {N}

   - Added {X} decisions
   - Added {Y} gotchas
   - Added {Z} key files

   Co-Authored-By: BuildSensei <noreply@buildsensei.dev>" 2>/dev/null
   ```
</step>

<step name="report">
## Report Extraction Results

Display summary of what was extracted and persisted:

```markdown
## Knowledge Extracted from Phase {N}

### Added to CLAUDE.md
- {X} decisions
- {Y} gotchas
- {Z} key files
- {W} patterns

### Added to skills.md
- {skill_count} new skills

### Added to references.md
- {ref_count} new references

### Updated in spec.md
- {feature_count} features marked complete

---
Context files updated and committed.
```
</step>

</process>

<success_criteria>
- [ ] Target phase identified correctly
- [ ] All SUMMARY.md files parsed
- [ ] Knowledge categorized appropriately
- [ ] CLAUDE.md updated with new knowledge
- [ ] No existing content overwritten
- [ ] Changes committed with descriptive message
- [ ] Extraction report displayed
</success_criteria>
