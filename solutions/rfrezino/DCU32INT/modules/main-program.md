---
type: Source Module
title: "dcu32int.dpr entry point and command line"
description: "dcu32int.dpr entry point and command line"
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: dcu32int.dpr
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# Main program

`dcu32int.dpr` is a console program. Usage: `DCU32INT <source file> <flags> [<destination file>]`. The destination may contain `*` (replaced by unit name) or be `-` for stdout.

# Flags

| Flag | Meaning |
|---|---|
| -S<x> | Show flags: A address table, C no const resolve, D data block, d dot types, F fixups, H heuristic strings (on), I imported names (on), L locals, M no method resolve, O file offsets, S self args, T type table, U units of imports, V aux values, v VMT |
| -O<x> | Code generation options: V typed constants as variables, S check unit stamps |
| -I | Interface part only |
| -U<paths> | Unit directories; * autodetects by Delphi version |
| -P<paths> | Pascal source dirs for source lines |
| -R | Unit aliases |
| -N, -D | No-name and dot-name prefixes |
| -F | Output format T text or H HTML |
| -o | Output file |
| -Q | Query: F class fields, V virtual methods |
| -A | Disassembler mode: S sequential (default), C control flow |

It pulls in the units described in [DCU input](/modules/dcu-input.md), [unit table](/modules/unit-table.md), [fixups](/modules/fixups.md), [DCP](/modules/dcp.md) and [disassemblers](/modules/disassemblers.md). See the [overview](/overview.md).


