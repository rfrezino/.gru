---
type: "Source Module"
title: "Disassemblers"
description: "x86/x64, MSIL and control-flow modules"
---
- `DasmDefs.pas`: shared constants (`nf`, `nm`, `hEA`), register debug-info callback `GetRegVarInfo`.
- `DasmX86.pas`: `Set80x86Disassembler` registers the Intel 32/64-bit disassembler (I64 mode).
- `DasmMSIL.pas`: ECMA-335 CIL method header (fat format) and opcode decoding for .NET units; uses [FixUp](/modules/fixup.md).
- `DasmCF.pas`: control-flow data structures for the disassembler.
Register names come from [op.pas](/modules/op.md).
