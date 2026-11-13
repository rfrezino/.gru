---
type: Module
tags: [projen]
title: Command line interface
description: Command line interface in projen
resource: src/cli/
generated:
  by: claude-code/sonnet-5.5
  at: 2026-11-13T00:00:00Z
sources:
  - id: src
    resource: src/cli/
---

# Command line interface

`bin/projen` entry; src/cli/cmds holds commands (new, synth, task, eject, etc.); synth.ts runs the projenrc file; macros.ts supports readme macros; util.ts shared helpers.

Related: [Project](/concepts/project.md), [Tasks](/concepts/tasks.md)
