---
type: "Overview"
title: "RFindUnit overview"
description: "What RFindUnit is and how its parts fit together."
---
# RFindUnit

A Delphi IDE plugin (Open Tools API package) by Rodrigo Farias Rezino that replaces the slow Find Unit dialog (Ctrl+Shift+A), and adds auto import of missing units, organize uses (Ctrl+Shift+U) and unused-uses detection (experimental).

## Flow
1. [IDE Integration](/components/ide-integration.md) registers keybindings and menus.
2. [Environment Controller](/components/environment-controller.md) scans the library path and project paths via the [Parser Worker](/components/parser-worker.md).
3. The worker parses `.pas` files with the [Pas Parser](/components/pas-parser.md) (DelphiAST); `.dcu` files are first decompiled by the [DCU Decompiler](/components/dcu-decompiler.md).
4. Results live in the [Units Cache](/components/units-cache.md) and are queried for search, [Auto Import](/components/auto-import.md) and [Unused Uses](/components/unused-uses.md).

## Layout
- `Source/` plugin units (`FindUnit.*.pas`, forms `.dfm`)
- `Interfaces/` `Interf.*` interfaces
- `ideInteraction/implementations/` IDE painting of unused uses
- `Test/` DUnit tests (parser, search string, unused uses)
- `Packages/` per-Delphi-version packages
- `Thirdy/` bundled libraries, see [Third-party](/components/third-party.md)
