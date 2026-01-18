---
name: sensei:rollback-context
description: Restore context files from backup
argument-hint: "[timestamp]"
allowed-tools: [Read, Write, Bash, Glob, AskUserQuestion]
---

<objective>
Restore context files from a previous backup.

Use when a context file regeneration went wrong or you want to revert to a previous state.
Lists available backups and lets you choose which to restore.
</objective>

<context>
$ARGUMENTS - Optional timestamp to restore (e.g., 20260118_143022)

Backup location: .planning/backups/
</context>

<process>

<step name="list_backups">
## List Available Backups

```bash
ls -la .planning/backups/*.md 2>/dev/null | head -30
```

If no backups found:
```
No backups found in .planning/backups/

Backups are created automatically when:
- /sensei:extract-knowledge runs
- /sensei:refresh-context runs
- Context files are updated

Nothing to restore.
```
Exit if no backups.
</step>

<step name="select_backup">
## Select Backup to Restore

If $ARGUMENTS contains a timestamp:
- Use that timestamp
- Verify backup exists

Otherwise:
- Parse available backups
- Group by timestamp
- Present options using AskUserQuestion

```
Available backup sets:

1. 2026-01-18 14:30:22 (5 files)
   - CLAUDE_20260118_143022.md
   - spec_20260118_143022.md
   - PRD_20260118_143022.md
   - skills_20260118_143022.md
   - references_20260118_143022.md

2. 2026-01-18 12:15:08 (3 files)
   - CLAUDE_20260118_121508.md
   - spec_20260118_121508.md
   - PRD_20260118_121508.md

3. 2026-01-17 18:45:33 (2 files)
   - CLAUDE_20260117_184533.md
   - spec_20260117_184533.md
```

Use AskUserQuestion:
- header: "Restore"
- question: "Which backup set do you want to restore?"
- options: List of timestamps with file counts
</step>

<step name="confirm">
## Confirm Restoration

Show what will be restored:

```markdown
## Restore Confirmation

**Timestamp:** {selected_timestamp}
**Files to restore:**
- CLAUDE.md ← CLAUDE_{timestamp}.md
- spec.md ← spec_{timestamp}.md
- PRD.md ← PRD_{timestamp}.md
(etc.)

**Current files will be backed up first.**

Proceed?
```

Use AskUserQuestion:
- header: "Confirm"
- question: "Restore these files?"
- options:
  - "Restore" - Proceed with restoration
  - "Cancel" - Keep current files
</step>

<step name="restore">
## Restore Files

1. Backup current files (safety net):
   ```bash
   TIMESTAMP=$(date +%Y%m%d_%H%M%S)
   for file in CLAUDE.md spec.md PRD.md agents.md skills.md references.md; do
     if [ -f "$file" ]; then
       cp "$file" ".planning/backups/${file%.md}_PRE_RESTORE_${TIMESTAMP}.md"
     fi
   done
   ```

2. Restore from selected backup:
   ```bash
   for backup in .planning/backups/*_{selected_timestamp}.md; do
     filename=$(basename "$backup" | sed "s/_${selected_timestamp}//" | sed 's/\.md$/.md/')
     cp "$backup" "$filename"
     echo "Restored: $filename"
   done
   ```

3. Commit restoration:
   ```bash
   git add CLAUDE.md spec.md PRD.md agents.md skills.md references.md 2>/dev/null
   git commit -m "docs(context): Restore context files from backup

   Restored from backup: {selected_timestamp}
   Pre-restore backup: {safety_timestamp}

   Co-Authored-By: BuildSensei <noreply@buildsensei.dev>" 2>/dev/null
   ```
</step>

<step name="report">
## Report Results

```markdown
## Context Files Restored

**From backup:** {selected_timestamp}

| File | Status |
|------|--------|
| CLAUDE.md | Restored |
| spec.md | Restored |
| PRD.md | Restored |
| agents.md | Restored |
| skills.md | Restored |
| references.md | Restored |

**Safety backup created:** .planning/backups/*_PRE_RESTORE_{timestamp}.md

If this restoration was wrong, run:
`/sensei:rollback-context {safety_timestamp}`
```
</step>

</process>

<success_criteria>
- [ ] Available backups listed
- [ ] User selected backup to restore
- [ ] Current files backed up (safety net)
- [ ] Selected backup restored
- [ ] Changes committed
- [ ] Report displayed
</success_criteria>
