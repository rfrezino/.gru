---
type: Module
tags: [projen]
title: Tasks
description: Tasks in projen
resource: src/tasks.ts
generated:
  by: claude-code/sonnet-5.5
  at: 2026-11-13T00:00:00Z
sources:
  - id: src
    resource: src/tasks.ts
---

# Tasks

Task model (src/task.ts, task-model.ts, task-runtime.ts). Tasks are stored in `.projen/tasks.json` and executed by the task runtime via `projen <task>`; steps may be exec commands, builtins or sub-tasks. Generated package.json scripts delegate to projen.bash.

Related: [Project](/concepts/project.md), [CLI](/concepts/cli.md)
