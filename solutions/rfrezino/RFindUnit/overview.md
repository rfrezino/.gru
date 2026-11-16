---
type: Overview
title: RFindUnit overview
description: Delphi IDE plugin that finds units, auto-imports, organizes uses and flags unused uses
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: README.md
---

RFindUnit is a Delphi IDE expert (Delphi Seattle/Berlin/Tokyo) that replaces the slow Find Unit dialog (Ctrl+Shift+A).

# Features
- Find unit: fast search of declared identifiers, handled by [search](/components/search.md).
- Auto import of missing units: [auto-import](/components/auto-import.md).
- Auto organize uses (Ctrl+Shift+U), sorted and split per namespace.
- Unused uses detection (experimental): [unused-uses](/components/unused-uses.md).

# Architecture
- [Plugin entry](/components/plugin-entry.md) registers keyboard bindings and wires the [environment controller](/components/environment-controller.md).
- [Parser worker](/components/parser-worker.md) scans project and library paths using the [Pascal parser](/components/pas-parser.md) (built on DelphiAST) and fills the [unit cache](/components/unit-cache.md).
- [DCU decompiler](/components/dcu-decompiler.md) reads compiled units via Dcu32Int when sources are unavailable.
- [Build and packaging](/components/build-and-packaging.md) and [third-party code](/components/third-party.md) and [tests](/components/tests.md).
