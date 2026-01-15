---
name: sensei
description: Interactive BuildSensei menu - shows available commands and suggests next steps
allowed-tools: [Read, Glob, AskUserQuestion]
---

<objective>
Show an interactive menu to help users discover and navigate BuildSensei commands.

Detects project state and suggests the most relevant next action.
</objective>

<context>
Check for existing project files:
```bash
ls .planning/PROJECT.md 2>/dev/null && echo "HAS_PROJECT"
ls .planning/ROADMAP.md 2>/dev/null && echo "HAS_ROADMAP"
ls .planning/phases/*/PLAN.md 2>/dev/null && echo "HAS_PLANS"
ls .planning/phases/*/SUMMARY.md 2>/dev/null && echo "HAS_SUMMARIES"
```
</context>

<process>

<step name="detect_state">
## Detect Project State

Based on which files exist, determine the user's position:

1. **No .planning/ directory** → New user, no project started
2. **PROJECT.md exists, no ROADMAP.md** → Project defined, needs roadmap
3. **ROADMAP.md exists, no PLAN.md files** → Roadmap created, needs planning
4. **PLAN.md exists, no SUMMARY.md** → Planned, needs execution
5. **SUMMARY.md exists** → Has executed, check for completion
</step>

<step name="show_menu">
## Show Interactive Menu

Display menu based on detected state:

### If No Project Exists

```markdown
# BuildSensei

No project detected. Let's get started!

**Recommended:** Start a new project
`/sensei:new-project`

Or if you have existing code:
`/sensei:map-codebase`
```

Use AskUserQuestion:
- "Start new project" → Run /sensei:new-project
- "Map existing codebase" → Run /sensei:map-codebase
- "Show all commands" → Run /sensei:help

### If Project Exists

```markdown
# BuildSensei

## What would you like to do?

### Project Setup
- `/sensei:new-project` — Start a new project
- `/sensei:research-project` — Research domain ecosystem
- `/sensei:define-requirements` — Scope v1/v2 requirements
- `/sensei:create-roadmap` — Create phased roadmap

### Execution
- `/sensei:plan-phase [N]` — Plan a phase
- `/sensei:execute-phase [N]` — Execute a phase
- `/sensei:progress` — Check current status

### Verification
- `/sensei:verify-work` — Test completed work
- `/sensei:plan-fix` — Plan fixes for issues

### Session
- `/sensei:pause-work` — Save progress and stop
- `/sensei:resume-work` — Continue from last session

---

**Suggested next step based on your project:**
[Dynamic suggestion based on state]
```

Use AskUserQuestion with relevant options based on detected state.
</step>

<step name="suggest_next">
## Provide Context-Aware Suggestion

Based on project state, highlight the recommended action:

| State | Suggestion |
|-------|------------|
| No project | `/sensei:new-project` |
| Project, no requirements | `/sensei:define-requirements` |
| Requirements, no roadmap | `/sensei:create-roadmap` |
| Roadmap, phase not planned | `/sensei:plan-phase [next]` |
| Phase planned, not executed | `/sensei:execute-phase [N]` |
| Phase executed, not verified | `/sensei:verify-work` |
| All phases complete | `/sensei:complete-milestone` |

Present suggestion prominently with copy-paste command.
</step>

<step name="handle_selection">
## Handle User Selection

If user selects an action via AskUserQuestion:
- Acknowledge selection
- Provide the exact command to run
- Offer brief context on what it does

Example:
```markdown
**You selected: Plan Phase 3**

Run this command:
`/sensei:plan-phase 3`

This will analyze Phase 3 goals and create detailed task plans.
```
</step>

</process>

<success_criteria>
- [ ] Menu displays based on project state
- [ ] Suggested action is contextually appropriate
- [ ] User can easily navigate to next command
- [ ] New users understand where to start
</success_criteria>
