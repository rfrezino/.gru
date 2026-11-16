---
type: Usage
title: "Batch and shell scripts"
description: "Batch and shell scripts"
tags: [dcu32int]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: src
    resource: all.bat
usage_window: {from: "2026-11-16", to: "2026-11-16"}
---

# Scripts

The many all*.bat, alllib*.bat and run*.bat files are the author's personal batch runners. Each loops DCU32INT over a directory of .dcu files for a particular Delphi version (for example alllib3 targets Delphi 3, alllibK Kylix) and appends output to a `res` file. `alllibK.sh` does so on Linux for Kylix libs, and `selftst.sh` runs the tool on its own DCUs. Paths are hard-coded to the author's machines. See [overview](/overview.md).


