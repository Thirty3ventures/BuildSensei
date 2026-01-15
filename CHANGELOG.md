# Changelog

All notable changes to BuildSensei will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [1.1.0] - 2026-01-15

### Added

**Command Discovery UX**
- `/sensei` (no args) — Interactive menu that detects project state and suggests next steps
- `/sensei:sp` — Alias for `/sensei:plan-phase`
- `/sensei:ep` — Alias for `/sensei:execute-phase`
- `/sensei:prog` — Alias for `/sensei:progress`
- `/sensei:np` — Alias for `/sensei:new-project`
- `/sensei:cr` — Alias for `/sensei:create-roadmap`

**Documentation**
- Workflow diagram in `/sensei:help` showing typical BuildSensei flow
- Command aliases table in help and README
- Interactive action selection in `/sensei:progress`

### Changed
- `/sensei:progress` now offers quick action buttons after displaying status
- `/sensei:help` includes visual workflow diagram and aliases section

---

## [1.0.0] - 2026-01-15

### Initial Release

BuildSensei is a meta-prompting and context engineering framework for Claude Code, designed to transform chaotic vibe-coding into structured builds that actually ship.

### Features

**Project Setup**
- `/sensei:new-project` — Interactive project ideation with smart questioning
- `/sensei:research-project` — Parallel domain research (stack, features, architecture, pitfalls)
- `/sensei:define-requirements` — Scope v1/v2/out-of-scope with traceability
- `/sensei:create-roadmap` — Generate phased roadmap mapped to requirements
- `/sensei:map-codebase` — Analyze existing codebases (7 analysis documents)

**Execution**
- `/sensei:plan-phase` — Generate atomic task plans (2-3 tasks max per plan)
- `/sensei:execute-phase` — Parallel execution with wave-based grouping
- `/sensei:execute-plan` — Interactive single-plan execution with checkpoints
- `/sensei:progress` — Status tracking and next-action routing

**Verification**
- `/sensei:verify-work` — User acceptance testing workflow
- `/sensei:plan-fix` — Plan fixes for UAT issues
- `/sensei:audit-milestone` — Milestone completion verification

**Milestones**
- `/sensei:complete-milestone` — Archive and ship milestone
- `/sensei:discuss-milestone` — Gather context for next milestone
- `/sensei:new-milestone` — Create new milestone with phases
- `/sensei:plan-milestone-gaps` — Plan work for deferred items

**Phase Management**
- `/sensei:add-phase` — Append phase to roadmap
- `/sensei:insert-phase` — Insert urgent work as decimal phase (e.g., 7.1)
- `/sensei:remove-phase` — Remove phase and renumber subsequent
- `/sensei:discuss-phase` — Pre-planning context gathering
- `/sensei:research-phase` — Deep domain research
- `/sensei:list-phase-assumptions` — Surface Claude's assumptions

**Session Management**
- `/sensei:pause-work` — Create context handoff mid-phase
- `/sensei:resume-work` — Restore full context from previous session

**Utilities**
- `/sensei:add-todo` — Capture ideas and tasks
- `/sensei:check-todos` — List and work on pending todos
- `/sensei:debug` — Systematic debugging with state persistence
- `/sensei:help` — Complete command reference
- `/sensei:whats-new` — Show changes since installed version

### Subagents

- **sensei-executor** — Autonomous plan execution with per-task atomic commits
- **sensei-verifier** — Goal-backward phase verification (starts from goals, works backward)
- **sensei-integration-checker** — Integration and completeness validation

### Architecture

- **Context Engineering** — Fresh 200k token context per subagent, zero degradation
- **XML Prompt Formatting** — Optimized for Claude's parsing
- **Atomic Git Commits** — One commit per task, full traceability
- **Living Memory** — STATE.md persists context across sessions
- **Progressive Disclosure** — Command → Workflow → Template → Reference

### Context Files

| File | Purpose |
|------|---------|
| `PROJECT.md` | Project vision, always loaded |
| `REQUIREMENTS.md` | Scoped v1/v2 requirements |
| `ROADMAP.md` | Phase breakdown with progress |
| `STATE.md` | Living memory, decisions, blockers |
| `PLAN.md` | Atomic task with XML structure |
| `SUMMARY.md` | Execution results, committed to history |
| `research/` | Domain knowledge (stack, features, pitfalls) |
| `codebase/` | Brownfield project analysis |
| `todos/` | Captured ideas for later |

---

*Stop the chaos. Ship clean code.*
