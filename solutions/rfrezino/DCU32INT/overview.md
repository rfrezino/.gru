---
type: Project
title: DCU32INT overview
description: Delphi/Kylix DCU to Pascal-like interface converter
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: readme.txt
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# DCU32INT

Utility by Alexei Hmelnov (v1.20.1) that parses compiled Delphi units (DCU, DPU, DCUIL) and packages (DCP, DCPIL) from Delphi 2 through XE5 and Kylix 1-3, and emits close-to-Pascal text (or HTML). It recovers unit interfaces, not full source. It is a by-product of the FlexT project.

Targets: WIN32, WIN64, OSX32, .NET (MSIL), iOS simulator; iOS device and Android units lack code because the code lives in separate .o files, which are not processed.

Main parts:
- [Main program and CLI](/modules/main-program.md)
- [DCU input reader](/modules/dcu-input.md)
- [Unit table](/modules/unit-table.md)
- [Fixups](/modules/fixups.md)
- [Package support](/modules/dcp.md)
- [Disassemblers](/modules/disassemblers.md)
- [Opcode tables](/modules/opcode-tables.md)
- [Batch scripts](/usage/batch-scripts.md)
- [Build configuration](/usage/build-config.md)

Source files DCU32.pas, DCU_Out.pas and DCURecs.pas are listed in the project uses clause but are not present in this checkout.
