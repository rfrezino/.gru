---
type: Source Module
title: "Disassembler framework: definitions, control flow, x86, MSIL"
description: "Disassembler framework: definitions, control flow, x86, MSIL"
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: DasmDefs.pas
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# Disassemblers

- DasmDefs.pas: generic basic definitions.
- DasmCF.pas: control-flow information data structures (used by -AC mode).
- DasmX86.pas: registers the i80x86 / Intel 64 disassembler (uses [opcode tables](/modules/opcode-tables.md)).
- DasmMSIL.pas: MSIL disassembler for .NET units.

Selected by the -A option; see [main program](/modules/main-program.md).


