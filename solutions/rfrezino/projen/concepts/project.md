---
type: Module
tags: [projen]
title: Project and synthesis
description: Project and synthesis in projen
resource: src/project.ts
generated:
  by: claude-code/sonnet-5.5
  at: 2026-11-13T00:00:00Z
sources:
  - id: src
    resource: src/project.ts
---

# Project and synthesis

Root of the in-memory model. A Project owns Components, tasks (`project.tasks`), dependencies (`project.deps`) and files. `synth()` runs: project preSynthesize, component preSynthesize, cleanup of previously generated files, component synthesize, component postSynthesize, project postSynthesize. Only projen-managed files are cleaned up. See ARCHITECTURE.md.

Related: [Components](/concepts/component.md), [Tasks](/concepts/tasks.md), [CLI](/concepts/cli.md)
