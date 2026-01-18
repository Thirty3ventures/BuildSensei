<knowledge-entry-template>

<purpose>
Template for knowledge entries extracted after each phase.
These entries are appended to CLAUDE.md and used to update other context files.
</purpose>

<template>
## Phase {phase_number}: {phase_name}

**Completed:** {completion_date}
**Duration:** {duration}

### Decisions Made
{decisions_list}

### Gotchas Discovered
{gotchas_list}

### Patterns Found
{patterns_list}

### Key Files Modified
{files_list}

### Dependencies Added
{dependencies_list}

### Technical Notes
{technical_notes}
</template>

<extraction_sources>
## Where to Extract From

### SUMMARY.md Files
- `decisions` section → Decisions Made
- `deviations` section → Gotchas Discovered
- `files_modified` frontmatter → Key Files Modified
- `dependencies` mentions → Dependencies Added

### VERIFICATION.md
- `gaps` section → What didn't work initially
- `fixes` section → How issues were resolved

### Code Analysis
- Repeated patterns → Patterns Found
- Import statements → Dependencies Added
- Complex logic with comments → Technical Notes
</extraction_sources>

<categorization_rules>
## How to Categorize Extracted Knowledge

### Decisions (goes to CLAUDE.md Key Decisions table)
Pattern: "Chose X over Y because Z"
Examples:
- "Used JWT instead of sessions for stateless auth"
- "Went with PostgreSQL over MongoDB for relational data"

### Gotchas (goes to CLAUDE.md Gotchas section)
Pattern: "X didn't work because Y, fixed by Z"
Examples:
- "Stripe webhooks require raw body, not parsed JSON"
- "React 18 strict mode causes double renders in dev"

### Patterns (goes to skills.md if reusable)
Pattern: "Created reusable X that handles Y"
Examples:
- "Auth middleware pattern for protected routes"
- "Form validation hook with error handling"

### Key Files (goes to CLAUDE.md Key Files table)
Pattern: "Modified X to do Y"
Examples:
- "src/middleware/auth.ts - JWT validation"
- "src/hooks/useForm.ts - Form state management"
</categorization_rules>

<merge_rules>
## Merge Rules for CLAUDE.md Updates

1. **Decisions Table:** Append new rows, never modify existing
2. **Gotchas List:** Append new items, check for duplicates
3. **Patterns List:** Append new items, link to skills.md if extracted
4. **Key Files Table:** Append new files, update existing if purpose changed
5. **Current Sprint Context:** Replace entirely with latest state
6. **Session Continuity:** Replace entirely with latest state
</merge_rules>

</knowledge-entry-template>
