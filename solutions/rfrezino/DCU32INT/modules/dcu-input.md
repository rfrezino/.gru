---
type: Source Module
title: "Low-level DCU binary reader"
description: "Low-level DCU binary reader"
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: DCU_In.pas
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# DCU_In

Low-level readers over the mapped DCU image: ReadTag, ReadByte/Word/ULong, ReadStr, ReadName (TNameRec supports names over 255 bytes, needed since D2009), variable-length ReadIndex/ReadUIndex (and 64-bit variants), NDX string references, and MemToInt helpers. Also error and warning reporting (DCUError, DCUWarning, TagError) and scan-state save/restore (ChangeScanState) for nested block parsing. Used by [main program](/modules/main-program.md).


