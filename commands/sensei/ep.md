---
name: sensei:ep
description: Alias for /sensei:execute-phase
argument-hint: "<phase-number>"
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion, Task, TodoWrite]
---

<objective>
Shortcut alias for `/sensei:execute-phase`. Runs the full execute-phase command.
</objective>

<execution_context>
@~/.claude/commands/sensei/execute-phase.md
</execution_context>

<process>
Run `/sensei:execute-phase $ARGUMENTS`

This is an alias — execute the full execute-phase command with all provided arguments.
</process>
