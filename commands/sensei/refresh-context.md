---
name: sensei:refresh-context
description: Regenerate context files from current project state
argument-hint: "[--file <name>]"
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

<objective>
Regenerate context files from current project state.

Use when context files are outdated, corrupted, or you want a fresh generation.
By default regenerates all context files. Use --file to regenerate a specific file.

Preserves user-edited sections when possible.
</objective>

<execution_context>
@~/.claude/buildsensei/workflows/generate-context-files.md
@~/.claude/buildsensei/workflows/extract-knowledge.md
</execution_context>

<context>
$ARGUMENTS - Optional --file flag with file name (CLAUDE.md, spec.md, PRD.md, agents.md)

Project files:
@.planning/PROJECT.md
@.planning/REQUIREMENTS.md
@.planning/ROADMAP.md
@.planning/STATE.md
</context>

<process>

<step name="validate">
## Validate Project Exists

```bash
[ -f .planning/PROJECT.md ] || { echo "ERROR: No project found. Run /sensei:new-project first."; exit 1; }
```
</step>

<step name="backup">
## Create Backups

Before regenerating, backup existing context files:

```bash
mkdir -p .planning/backups
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

for file in CLAUDE.md spec.md PRD.md agents.md skills.md references.md; do
  if [ -f "$file" ]; then
    cp "$file" ".planning/backups/${file%.md}_${TIMESTAMP}.md"
    echo "Backed up: $file"
  fi
done
```
</step>

<step name="determine_scope">
## Determine Regeneration Scope

If $ARGUMENTS contains "--file":
- Extract file name
- Regenerate only that file

Otherwise:
- Regenerate all context files
</step>

<step name="regenerate">
## Regenerate Context Files

For each file in scope:

### CLAUDE.md
1. Read PROJECT.md for overview
2. Read all SUMMARY.md files for decisions, gotchas, patterns
3. Read VERIFICATION.md files for lessons learned
4. Read STATE.md for current position
5. Generate fresh CLAUDE.md preserving user-edited sections
6. If user edits exist, merge carefully

### spec.md
1. Read REQUIREMENTS.md for features
2. Check ROADMAP.md for phase assignments
3. Check SUMMARY.md files for completion status
4. Generate fresh spec.md with current status

### PRD.md
1. Read PROJECT.md for vision
2. Read REQUIREMENTS.md for features
3. Read ROADMAP.md for timeline
4. Generate fresh PRD.md

### agents.md
1. Determine project type from tech stack
2. Generate agents based on what was built
3. Include context references

### skills.md
1. Scan codebase for reusable patterns
2. Read SUMMARY.md files for pattern discoveries
3. Generate skills from patterns found

### references.md
1. Read package.json/requirements.txt for dependencies
2. Generate doc links for major dependencies
3. Add any URLs found in code comments
</step>

<step name="commit">
## Commit Regenerated Files

```bash
git add CLAUDE.md spec.md PRD.md agents.md skills.md references.md 2>/dev/null
git commit -m "docs(context): Refresh context files

Regenerated context files from current project state.
Backups preserved at .planning/backups/

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>" 2>/dev/null
```
</step>

<step name="report">
## Report Results

```markdown
## Context Files Refreshed

| File | Status | Sections |
|------|--------|----------|
| CLAUDE.md | Regenerated | {section count} |
| spec.md | Regenerated | {feature count} features |
| PRD.md | Regenerated | {section count} |
| agents.md | Regenerated | {agent count} agents |
| skills.md | Regenerated | {skill count} skills |
| references.md | Regenerated | {ref count} references |

**Backups:** .planning/backups/*_{timestamp}.md

---

To restore previous version:
`/sensei:rollback-context`
```
</step>

</process>

<success_criteria>
- [ ] Existing files backed up
- [ ] Context files regenerated from current state
- [ ] User-edited sections preserved where possible
- [ ] Changes committed
- [ ] Report displayed
</success_criteria>
