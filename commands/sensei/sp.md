---
name: sensei:sp
description: Alias for /sensei:plan-phase
argument-hint: "[phase-number]"
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion, Task, TodoWrite]
---

<objective>
Shortcut alias for `/sensei:plan-phase`. Runs the full plan-phase command.
</objective>

<execution_context>
@~/.claude/commands/sensei/plan-phase.md
</execution_context>

<process>
Run `/sensei:plan-phase $ARGUMENTS`

This is an alias — execute the full plan-phase command with all provided arguments.
</process>
