---
type: Module
tags: [projen]
title: Component
description: Component in projen
resource: src/component.ts
generated:
  by: claude-code/sonnet-5.5
  at: 2026-11-13T00:00:00Z
sources:
  - id: src
    resource: src/component.ts
---

# Component

Base class for building blocks attached to a project (files, tasks, eslint, jest, ...). Hooks: preSynthesize, synthesize, postSynthesize. Files are components such as JsonFile, TextFile, IgnoreFile, YamlFile-like ObjectFile, Makefile, SampleFile (src/file.ts, json.ts, textfile.ts, ignore-file.ts, object-file.ts).

Related: [Project](/concepts/project.md)
