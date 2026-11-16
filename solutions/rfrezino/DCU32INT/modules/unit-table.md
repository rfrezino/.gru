---
type: Source Module
title: "Table of used units"
description: "Table of used units"
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: DCUTbl.pas
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# DCUTbl

Maintains the table of units used by the unit being decoded, to obtain imported declarations. If an imported unit is not found, the program still works but, for example, shows constants as hex dumps. Directories come from the -U option. See [main program](/modules/main-program.md).


