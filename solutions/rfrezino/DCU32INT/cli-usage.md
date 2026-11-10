---
type: "Command-line interface"
title: "CLI usage"
description: "Arguments and switches of dcu32int"
---
# Usage

`DCU32INT <Source file> <Switches> [<Destination file>]`

- Destination may contain `*`, replaced by the unit name (trailing `*` gives `<Unit>.int`); `-` writes to stdout.
- Switches start with `/` or `-`.
- `-S<flags>` show flags: A address table, C don't resolve constants, D data block, d dot types, F fixups, H heuristic strings (default on), I imported names (default on), L locals, M don't resolve methods, O file offsets, S Self args, T type table, U units of imported names, V aux values, v VMT.
- `-O<option>` code generation options.
- `-Q` outputs class field tables with offsets and virtual method tables with VMT offsets.
- `-U*` library path autodetection (default on); `-U<path>` overrides.
- `-P` requests source lines.

Defaults live in `dcu32int.conf` (compiler-style switches such as `-$A8`, `-K$00400000`). See [overview](/overview.md).
