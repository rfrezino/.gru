---
type: Configuration
title: "Build files and compiler switches"
description: "Build files and compiler switches"
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: dcu32int.conf
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# Build configuration

`dcu32int.dpr` and `dcu32int.dproj` build the tool; `dcu32int.conf` holds Delphi compiler switches (-$A8, -$H+, -cg, -W+ and so on). The code supports Unicode Delphi (XE2+) and Kylix via the LINUX define. DOSFTIME.RFI and UNIXTIME.RFI are time-conversion data files.


