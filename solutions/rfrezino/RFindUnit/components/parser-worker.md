---
type: Component
title: Parser worker
description: Background worker parsing Pascal files into the unit cache
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.Worker.pas
---

`TParserWorker` runs the background scan of project and library paths, parsing files with the [Pascal parser](/components/pas-parser.md), falling back to the [DCU decompiler](/components/dcu-decompiler.md), and storing results in the [unit cache](/components/unit-cache.md). `FindUnit.IncluderHandlerInc` resolves `{$I}` include files.
