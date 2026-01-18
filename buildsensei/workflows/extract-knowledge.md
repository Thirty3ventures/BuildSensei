<extract-knowledge-workflow>

<purpose>
Detailed workflow for extracting knowledge from completed phases and persisting
to project context files. Called by /sensei:extract-knowledge command and
automatically triggered after /sensei:execute-phase.
</purpose>

<when_to_use>
- Automatically after each phase execution (via execute-phase)
- Manually to extract from a specific completed phase
- To re-extract after manual fixes or additions
</when_to_use>

<required_reading>
@templates/context-files/knowledge-entry.md - Knowledge categorization rules
@templates/context-files/claude-md.md - CLAUDE.md structure and update rules
@templates/context-files/skills-md.md - Skills extraction and format
@templates/context-files/references-md.md - References detection patterns
</required_reading>

<inputs>
- Phase number (explicit or from STATE.md)
- SUMMARY.md files from phase directory
- VERIFICATION.md (if exists)
- Existing context files (CLAUDE.md, spec.md, skills.md, references.md)
</inputs>

<outputs>
- Updated CLAUDE.md with new knowledge
- Updated spec.md with feature status
- Updated skills.md (if patterns found)
- Updated references.md (if new references found)
- Backup files in .planning/backups/
- Git commit with changes
</outputs>

<process>

<step name="validate_prerequisites" priority="critical">
## Validate Prerequisites

Before extraction, verify:

1. **.planning/ directory exists:**
   ```bash
   test -d .planning || exit "No BuildSensei project found"
   ```

2. **Phase has completion artifacts (SUMMARY files use pattern XX-YY-SUMMARY.md):**
   ```bash
   ls .planning/phases/{XX}-*/*-SUMMARY.md 2>/dev/null
   ```

3. **Git is available (for commits):**
   ```bash
   git status 2>/dev/null
   ```

If prerequisites fail, exit with helpful error message.
</step>

<step name="parse_summary_files" priority="critical">
## Parse SUMMARY.md Files

For each SUMMARY.md in the target phase:

### Extract Frontmatter
```yaml
---
phase: XX-name
plan: NN
subsystem: component-name
tags: [tag1, tag2]
key-files:
  created: [file1.ts, file2.ts]
  modified: [file3.ts, file4.ts]
tech-stack:
  added: [package1, package2]
  patterns: [pattern1, pattern2]
key-decisions:
  - "Decision 1"
  - "Decision 2"
patterns-established:
  - "Pattern 1: description"
duration: Xm
completed: YYYY-MM-DD
---
```

### Extract Body Sections

**What Was Built:**
- Feature descriptions
- Implementation details

**Decisions Made:**
Look for pattern: `**Decision:** {description}`
Or table format in decisions section

**Deviations from Plan:**
- Auto-fixes applied
- Workarounds implemented
- Issues encountered

**Task Commits:**
- List of commits made
- What each commit did
</step>

<step name="parse_verification" priority="normal">
## Parse VERIFICATION.md (if exists)

### Extract Verification Results
```yaml
---
status: passed | gaps_found | human_needed
score: X/Y
---
```

### Extract Gaps (if any)
- What didn't work initially
- How it was fixed
- Lessons learned

### Extract Must-Haves Verified
- Which acceptance criteria passed
- Which required re-work
</step>

<step name="categorize_knowledge" priority="critical">
## Categorize Extracted Knowledge

### Category: Decisions
**Pattern matching:**
- "Decided to...", "Chose X over Y", "Went with..."
- Explicit "Decision:" labels
- "Because...", "For...", "To enable..."

**Output format:**
```
| Decision | Choice | Rationale | Phase |
|----------|--------|-----------|-------|
| How to handle auth | JWT tokens | Stateless, scalable | 3 |
```

### Category: Gotchas
**Pattern matching:**
- "Deviation:", "Fixed:", "Workaround:"
- "Didn't work because...", "Had to..."
- "Unexpected:", "Issue:", "Problem:"
- Things that required auto-fix rules

**Output format:**
```
- Stripe webhooks need raw body parser, not JSON middleware (Phase 3)
- React 18 strict mode causes double effect runs in dev (Phase 2)
```

### Category: Patterns
**Pattern matching:**
- "Created helper/utility/hook for..."
- "Reusable pattern for..."
- "Abstracted X into Y"
- Code used in multiple files

**Output format:**
```
- Auth middleware pattern: src/middleware/auth.ts
- Form validation hook: src/hooks/useForm.ts
```

### Category: Key Files
**Source:** `key-files.created` and `key-files.modified` frontmatter

**Output format:**
```
| File | Purpose | Phase |
|------|---------|-------|
| src/middleware/auth.ts | JWT validation middleware | 3 |
```

### Category: Dependencies
**Source:** `tech-stack.added` frontmatter + npm/pip mentions in SUMMARY body

**Output format:**
```
- jsonwebtoken: JWT token generation/validation
- bcrypt: Password hashing
```

### Category: Technical Notes
**Pattern matching:**
- Performance considerations
- Security notes
- Edge cases
- Important implementation details

**Output format:**
Free-form text appended to relevant sections
</step>

<step name="merge_to_claude_md" priority="critical">
## Merge Knowledge to CLAUDE.md

### Read Current State
```bash
cat CLAUDE.md 2>/dev/null || echo "FILE_NOT_EXISTS"
```

### If File Doesn't Exist
Create skeleton from template:
- Project Overview (from PROJECT.md)
- Tech Stack (placeholder or from config)
- Empty sections with placeholders
- Metadata block

### Merge Rules

**Key Decisions Table:**
1. Find `## Key Decisions` section
2. Find table end (line before next ##)
3. Append new rows
4. Never modify existing rows

**Gotchas Section:**
1. Find `## Gotchas Discovered` section
2. Check for duplicate gotchas (fuzzy match)
3. Append new bullets
4. Keep chronological order

**Patterns Section:**
1. Find `## Patterns Discovered` section
2. Check for duplicates
3. Append new patterns
4. Link to skills.md if substantial

**Key Files Table:**
1. Find `## Key Files` section
2. Check if file already listed
3. If exists, update purpose if changed
4. If new, append row

**Current Sprint Context:**
1. Find section
2. Replace entirely with current state from STATE.md

**Session Continuity:**
1. Find section
2. Update with latest position

### Update Metadata
```markdown
<!--
  Last updated: {ISO timestamp}
  Current phase: {phase_number}
  phases_incorporated: [1, 2, 3, ...]
-->
```
</step>

<step name="update_other_files" priority="normal">
## Update Other Context Files

### spec.md Updates
1. Find features associated with this phase
2. Update status: "Pending" → "Complete"
3. Check acceptance criteria boxes
4. Update timeline table

### skills.md Updates (if patterns qualify)
Qualification criteria:
- Pattern used in 2+ places
- Self-contained implementation
- Clear usage pattern
- Tested (if applicable)

If qualified:
1. Create skill entry from template
2. Append to appropriate category
3. Include code example

### references.md Updates
Sources to check:
- New dependencies → find official docs
- API integrations → find API docs
- URLs in code comments
- External services configured

If new references found:
1. Categorize appropriately
2. Check for duplicates
3. Append to correct section
</step>

<step name="backup_files" priority="normal">
## Create Backups

Before writing updates:

```bash
mkdir -p .planning/backups
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

# Backup each file that will be modified
for file in CLAUDE.md spec.md skills.md references.md; do
  if [ -f "$file" ]; then
    cp "$file" ".planning/backups/${file%.md}_${TIMESTAMP}.md"
  fi
done
```

Keep only last 10 backups per file:
```bash
ls -t .planning/backups/CLAUDE_*.md | tail -n +11 | xargs rm -f 2>/dev/null
```
</step>

<step name="write_updates" priority="critical">
## Write Updates

Write each file atomically:

1. Write to temp file first
2. Validate content (not empty, has required sections)
3. Move temp to final location
4. Verify write succeeded

```bash
# Example for CLAUDE.md
echo "$NEW_CONTENT" > CLAUDE.md.tmp
if [ -s CLAUDE.md.tmp ]; then
  mv CLAUDE.md.tmp CLAUDE.md
else
  echo "ERROR: Empty content, aborting write"
  rm CLAUDE.md.tmp
fi
```
</step>

<step name="commit_changes" priority="normal">
## Commit Context Updates

Stage and commit all updated context files:

```bash
# Stage only context files (not .planning/)
git add CLAUDE.md spec.md skills.md references.md 2>/dev/null

# Check if there are changes to commit
if git diff --cached --quiet; then
  echo "No changes to commit"
else
  git commit -m "docs(context): Extract knowledge from Phase {N}

Updated context files with Phase {N} learnings:
- {X} new decisions documented
- {Y} gotchas recorded
- {Z} key files tracked
- {W} patterns identified

Co-Authored-By: BuildSensei <noreply@buildsensei.dev>"
fi
```
</step>

<step name="generate_report" priority="normal">
## Generate Extraction Report

Output summary for user:

```markdown
## Knowledge Extraction Complete

**Source:** Phase {N} - {phase_name}
**Files Processed:** {summary_count} SUMMARY.md files

### Extracted & Persisted

| Category | Count | Destination |
|----------|-------|-------------|
| Decisions | {X} | CLAUDE.md |
| Gotchas | {Y} | CLAUDE.md |
| Key Files | {Z} | CLAUDE.md |
| Patterns | {W} | CLAUDE.md / skills.md |
| Skills | {S} | skills.md |
| References | {R} | references.md |
| Features Complete | {F} | spec.md |

### Files Updated
- CLAUDE.md ✓
- spec.md ✓
- skills.md {✓ or "No new skills"}
- references.md {✓ or "No new references"}

### Backup Created
`.planning/backups/*_{timestamp}.md`

---
Knowledge persisted. Ready for next phase.
```
</step>

</process>

<error_handling>
## Error Handling

### No SUMMARY.md files found
```
Error: No completed phases found for extraction.
Run /sensei:execute-phase first to complete a phase.
```

### CLAUDE.md write fails
```
Error: Failed to update CLAUDE.md
Backup preserved at: .planning/backups/CLAUDE_{timestamp}.md
Please check file permissions and disk space.
```

### Git commit fails
```
Warning: Could not commit context updates.
Files have been updated locally.
Please commit manually: git add CLAUDE.md spec.md && git commit
```
</error_handling>

<integration_points>
## Integration with Other Commands

### Called by execute-phase
After verification completes, execute-phase calls:
```
/sensei:extract-knowledge {phase_number}
```

### Manual invocation
User can run directly:
```
/sensei:extract-knowledge      # Extract from latest phase
/sensei:extract-knowledge 3    # Extract from Phase 3
```

### Re-extraction
Safe to run multiple times - uses append-only logic:
- Won't duplicate entries (fuzzy matching)
- Won't overwrite user edits
- Will update metadata
</integration_points>

</extract-knowledge-workflow>
