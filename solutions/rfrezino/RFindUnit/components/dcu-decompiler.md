---
type: "Component"
title: "DCU Decompiler"
description: "Wraps dcu32int.exe to turn dcu into pas-like text."
---
# DCU Decompiler

`TDcuDecompiler` (`Source/FindUnit.DcuDecompiler.pas`) runs `dcu32int.exe` (from the Dcu32Int project) to generate declarations for `.dcu` files that lack source, so [Parser Worker](/components/parser-worker.md) can index them.
