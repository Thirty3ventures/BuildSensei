---
name: sensei:help
description: Show available BuildSensei commands and usage guide
---

<objective>
Display the complete BuildSensei command reference.

Output ONLY the reference content below. Do NOT add:

- Project-specific analysis
- Git status or file context
- Next-step suggestions
- Any commentary beyond the reference
  </objective>

<reference>
# BuildSensei Command Reference

**BuildSensei** (BuildSensei) creates hierarchical project plans optimized for solo agentic development with Claude Code.

## Quick Start

1. `/sensei:new-project` - Initialize project with brief
2. `/sensei:create-roadmap` - Create roadmap and phases
3. `/sensei:plan-phase <number>` - Create detailed plan for first phase
4. `/sensei:execute-plan <path>` - Execute the plan

**Tip:** Run `/sensei` (no arguments) for an interactive menu.

## Typical Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                        SETUP                                 │
│  new-project → research-project → define-requirements       │
│                        ↓                                     │
│                  create-roadmap                              │
└─────────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                    PER PHASE                                 │
│         discuss-phase → plan-phase → execute-phase          │
│                        ↓                                     │
│              verify-work → plan-fix (if needed)             │
└─────────────────────────────────────────────────────────────┘
                         ↓
              ┌──────────────────┐
              │ complete-milestone│
              └──────────────────┘
```

## Command Aliases

Quick shortcuts for common commands:

| Alias | Full Command | Description |
|-------|--------------|-------------|
| `/sensei:sp` | `/sensei:plan-phase` | Plan a phase |
| `/sensei:ep` | `/sensei:execute-phase` | Execute a phase |
| `/sensei:prog` | `/sensei:progress` | Check status |
| `/sensei:np` | `/sensei:new-project` | Start new project |
| `/sensei:cr` | `/sensei:create-roadmap` | Create roadmap |

## Staying Updated

BuildSensei evolves fast. Check for updates periodically:

```
/sensei:whats-new
```

Shows what changed since your installed version. Update with:

```bash
npx buildsensei@latest
```

## Core Workflow

```
Initialization → Planning → Execution → Milestone Completion
```

### Project Initialization

**`/sensei:new-project`**
Initialize new project with brief and configuration.

- Creates `.planning/PROJECT.md` (vision and requirements)
- Creates `.planning/config.json` (workflow mode)
- Asks for workflow mode (interactive/yolo) upfront
- Commits initialization files to git

Usage: `/sensei:new-project`

**`/sensei:create-roadmap`**
Create roadmap and state tracking for initialized project.

- Creates `.planning/ROADMAP.md` (phase breakdown)
- Creates `.planning/STATE.md` (project memory)
- Creates `.planning/phases/` directories

Usage: `/sensei:create-roadmap`

**`/sensei:map-codebase`**
Map an existing codebase for brownfield projects.

- Analyzes codebase with parallel Explore agents
- Creates `.planning/codebase/` with 7 focused documents
- Covers stack, architecture, structure, conventions, testing, integrations, concerns
- Use before `/sensei:new-project` on existing codebases

Usage: `/sensei:map-codebase`

### Phase Planning

**`/sensei:discuss-phase <number>`**
Help articulate your vision for a phase before planning.

- Captures how you imagine this phase working
- Creates CONTEXT.md with your vision, essentials, and boundaries
- Use when you have ideas about how something should look/feel

Usage: `/sensei:discuss-phase 2`

**`/sensei:research-phase <number>`**
Comprehensive ecosystem research for niche/complex domains.

- Discovers standard stack, architecture patterns, pitfalls
- Creates RESEARCH.md with "how experts build this" knowledge
- Use for 3D, games, audio, shaders, ML, and other specialized domains
- Goes beyond "which library" to ecosystem knowledge

Usage: `/sensei:research-phase 3`

**`/sensei:list-phase-assumptions <number>`**
See what Claude is planning to do before it starts.

- Shows Claude's intended approach for a phase
- Lets you course-correct if Claude misunderstood your vision
- No files created - conversational output only

Usage: `/sensei:list-phase-assumptions 3`

**`/sensei:plan-phase <number>`**
Create detailed execution plan for a specific phase.

- Generates `.planning/phases/XX-phase-name/XX-YY-PLAN.md`
- Breaks phase into concrete, actionable tasks
- Includes verification criteria and success measures
- Multiple plans per phase supported (XX-01, XX-02, etc.)

Usage: `/sensei:plan-phase 1`
Result: Creates `.planning/phases/01-foundation/01-01-PLAN.md`

### Execution

**`/sensei:execute-plan <path>`**
Execute a single PLAN.md file.

- Runs plan tasks sequentially
- Creates SUMMARY.md after completion
- Updates STATE.md with accumulated context
- Use for interactive execution with checkpoints

Usage: `/sensei:execute-plan .planning/phases/01-foundation/01-01-PLAN.md`

**`/sensei:execute-phase <phase-number>`**
Execute all unexecuted plans in a phase with parallel background agents.

- Analyzes plan dependencies and spawns independent plans concurrently
- Use when phase has 2+ plans and you want "walk away" execution
- Respects max_concurrent_agents from config.json

Usage: `/sensei:execute-phase 5`

Options (via `.planning/config.json` parallelization section):
- `max_concurrent_agents`: Limit parallel agents (default: 3)
- `skip_checkpoints`: Skip human checkpoints in background (default: true)
- `min_plans_for_parallel`: Minimum plans to trigger parallelization (default: 2)

### Roadmap Management

**`/sensei:add-phase <description>`**
Add new phase to end of current milestone.

- Appends to ROADMAP.md
- Uses next sequential number
- Updates phase directory structure

Usage: `/sensei:add-phase "Add admin dashboard"`

**`/sensei:insert-phase <after> <description>`**
Insert urgent work as decimal phase between existing phases.

- Creates intermediate phase (e.g., 7.1 between 7 and 8)
- Useful for discovered work that must happen mid-milestone
- Maintains phase ordering

Usage: `/sensei:insert-phase 7 "Fix critical auth bug"`
Result: Creates Phase 7.1

**`/sensei:remove-phase <number>`**
Remove a future phase and renumber subsequent phases.

- Deletes phase directory and all references
- Renumbers all subsequent phases to close the gap
- Only works on future (unstarted) phases
- Git commit preserves historical record

Usage: `/sensei:remove-phase 17`
Result: Phase 17 deleted, phases 18-20 become 17-19

### Milestone Management

**`/sensei:discuss-milestone`**
Figure out what you want to build in the next milestone.

- Reviews what shipped in previous milestone
- Helps you identify features to add, improve, or fix
- Routes to /sensei:new-milestone when ready

Usage: `/sensei:discuss-milestone`

**`/sensei:new-milestone <name>`**
Create a new milestone with phases for an existing project.

- Adds milestone section to ROADMAP.md
- Creates phase directories
- Updates STATE.md for new milestone

Usage: `/sensei:new-milestone "v2.0 Features"`

**`/sensei:complete-milestone <version>`**
Archive completed milestone and prepare for next version.

- Creates MILESTONES.md entry with stats
- Archives full details to milestones/ directory
- Creates git tag for the release
- Prepares workspace for next version

Usage: `/sensei:complete-milestone 1.0.0`

### Progress Tracking

**`/sensei:progress`**
Check project status and intelligently route to next action.

- Shows visual progress bar and completion percentage
- Summarizes recent work from SUMMARY files
- Displays current position and what's next
- Lists key decisions and open issues
- Offers to execute next plan or create it if missing
- Detects 100% milestone completion

Usage: `/sensei:progress`

### Session Management

**`/sensei:resume-work`**
Resume work from previous session with full context restoration.

- Reads STATE.md for project context
- Shows current position and recent progress
- Offers next actions based on project state

Usage: `/sensei:resume-work`

**`/sensei:pause-work`**
Create context handoff when pausing work mid-phase.

- Creates .continue-here file with current state
- Updates STATE.md session continuity section
- Captures in-progress work context

Usage: `/sensei:pause-work`

### Debugging

**`/sensei:debug [issue description]`**
Systematic debugging with persistent state across context resets.

- Gathers symptoms through adaptive questioning
- Creates `.planning/debug/[slug].md` to track investigation
- Investigates using scientific method (evidence → hypothesis → test)
- Survives `/clear` — run `/sensei:debug` with no args to resume
- Archives resolved issues to `.planning/debug/resolved/`

Usage: `/sensei:debug "login button doesn't work"`
Usage: `/sensei:debug` (resume active session)

### Todo Management

**`/sensei:add-todo [description]`**
Capture idea or task as todo from current conversation.

- Extracts context from conversation (or uses provided description)
- Creates structured todo file in `.planning/todos/pending/`
- Infers area from file paths for grouping
- Checks for duplicates before creating
- Updates STATE.md todo count

Usage: `/sensei:add-todo` (infers from conversation)
Usage: `/sensei:add-todo Add auth token refresh`

**`/sensei:check-todos [area]`**
List pending todos and select one to work on.

- Lists all pending todos with title, area, age
- Optional area filter (e.g., `/sensei:check-todos api`)
- Loads full context for selected todo
- Routes to appropriate action (work now, add to phase, brainstorm)
- Moves todo to done/ when work begins

Usage: `/sensei:check-todos`
Usage: `/sensei:check-todos api`

### Context Management

**`/sensei:extract-knowledge [phase-number]`**
Extract knowledge from completed phase and persist to context files.

- Parses SUMMARY.md files for decisions, gotchas, patterns
- Updates CLAUDE.md with accumulated knowledge
- Updates spec.md with feature completion status
- Runs automatically after each phase execution
- Can be run manually for specific phases

Usage: `/sensei:extract-knowledge` (latest phase)
Usage: `/sensei:extract-knowledge 3` (specific phase)

**`/sensei:refresh-context [--file <name>]`**
Regenerate context files from current project state.

- Rebuilds CLAUDE.md, spec.md, PRD.md from project artifacts
- Preserves user-edited sections when possible
- Creates backup before regenerating
- Use when context files are outdated or corrupted

Usage: `/sensei:refresh-context` (all files)
Usage: `/sensei:refresh-context --file CLAUDE.md` (specific file)

**`/sensei:rollback-context [timestamp]`**
Restore context files from a previous backup.

- Lists available backups with timestamps
- Restores selected backup set
- Creates safety backup before restoring

Usage: `/sensei:rollback-context` (interactive selection)
Usage: `/sensei:rollback-context 20260118_143022` (specific backup)

### Utility Commands

**`/sensei:help`**
Show this command reference.

**`/sensei:whats-new`**
See what's changed since your installed version.

- Shows installed vs latest version comparison
- Displays changelog entries for versions you've missed
- Highlights breaking changes
- Provides update instructions when behind

Usage: `/sensei:whats-new`

## Files & Structure

### Project Root (Context Files)
```
project/
├── CLAUDE.md             # AI context - project knowledge for Claude
├── spec.md               # Product specification - features & status
├── PRD.md                # Product requirements document
├── agents.md             # Project-specific agent definitions
├── skills.md             # Reusable patterns discovered
├── references.md         # Quick links to docs & APIs
└── .planning/            # BuildSensei execution artifacts
```

### Planning Directory
```
.planning/
├── PROJECT.md            # Project vision
├── ROADMAP.md            # Current phase breakdown
├── STATE.md              # Project memory & context
├── REQUIREMENTS.md       # Scoped requirements
├── config.json           # Workflow mode & gates
├── backups/              # Context file backups
├── todos/                # Captured ideas and tasks
│   ├── pending/          # Todos waiting to be worked on
│   └── done/             # Completed todos
├── debug/                # Active debug sessions
│   └── resolved/         # Archived resolved issues
├── codebase/             # Codebase map (brownfield projects)
│   ├── STACK.md          # Languages, frameworks, dependencies
│   ├── ARCHITECTURE.md   # Patterns, layers, data flow
│   ├── STRUCTURE.md      # Directory layout, key files
│   ├── CONVENTIONS.md    # Coding standards, naming
│   ├── TESTING.md        # Test setup, patterns
│   ├── INTEGRATIONS.md   # External services, APIs
│   └── CONCERNS.md       # Tech debt, known issues
└── phases/
    ├── 01-foundation/
    │   ├── 01-01-PLAN.md
    │   └── 01-01-SUMMARY.md
    └── 02-core-features/
        ├── 02-01-PLAN.md
        └── 02-01-SUMMARY.md
```

## Workflow Modes

Set during `/sensei:new-project`:

**Interactive Mode**

- Confirms each major decision
- Pauses at checkpoints for approval
- More guidance throughout

**YOLO Mode**

- Auto-approves most decisions
- Executes plans without confirmation
- Only stops for critical checkpoints

Change anytime by editing `.planning/config.json`

## Common Workflows

**Starting a new project:**

```
/sensei:new-project
/sensei:create-roadmap
/sensei:plan-phase 1
/sensei:execute-plan .planning/phases/01-foundation/01-01-PLAN.md
```

**Resuming work after a break:**

```
/sensei:progress  # See where you left off and continue
```

**Adding urgent mid-milestone work:**

```
/sensei:insert-phase 5 "Critical security fix"
/sensei:plan-phase 5.1
/sensei:execute-plan .planning/phases/05.1-critical-security-fix/05.1-01-PLAN.md
```

**Completing a milestone:**

```
/sensei:complete-milestone 1.0.0
/sensei:new-project  # Start next milestone
```

**Capturing ideas during work:**

```
/sensei:add-todo                    # Capture from conversation context
/sensei:add-todo Fix modal z-index  # Capture with explicit description
/sensei:check-todos                 # Review and work on todos
/sensei:check-todos api             # Filter by area
```

**Debugging an issue:**

```
/sensei:debug "form submission fails silently"  # Start debug session
# ... investigation happens, context fills up ...
/clear
/sensei:debug                                    # Resume from where you left off
```

## Getting Help

- Read `.planning/PROJECT.md` for project vision
- Read `.planning/STATE.md` for current context
- Check `.planning/ROADMAP.md` for phase status
- Run `/sensei:progress` to check where you're up to
  </reference>
