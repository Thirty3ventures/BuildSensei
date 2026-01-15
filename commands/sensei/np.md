---
name: sensei:np
description: Alias for /sensei:new-project
allowed-tools: [Read, Write, Bash, Glob, Grep, AskUserQuestion, TodoWrite]
---

<objective>
Shortcut alias for `/sensei:new-project`. Starts a new project.
</objective>

<execution_context>
@~/.claude/commands/sensei/new-project.md
</execution_context>

<process>
Run `/sensei:new-project $ARGUMENTS`

This is an alias — execute the full new-project command with all provided arguments.
</process>
