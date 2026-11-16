---
type: Component
title: Unused uses
description: TUnsedUsesProcessor finds unused units (experimental)
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.UnusedUses.pas
---

`TUnsedUsesProcessor` parses a file with the [Pascal parser](/components/pas-parser.md) and checks which used units contribute identifiers. Results are painted in the editor by `ideInteraction/implementations/FindUnit.PaintUnusedUses.pas`. Must be enabled as an experimental setting.
