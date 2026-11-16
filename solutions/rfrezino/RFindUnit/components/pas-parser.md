---
type: Component
title: Pascal parser
description: TPasFile/TPasFileParser extract units, types and identifiers
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.PasParser.pas
---

`TPasFileParser` uses [DelphiAST](/components/third-party.md) to build `TPasFile` models: unit name, uses lists, and declared classes, methods, fields and constants. Used by the [parser worker](/components/parser-worker.md) and [unused uses](/components/unused-uses.md). `FindUnit.DelphiReservedWords` filters reserved words.
