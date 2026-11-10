---
type: "Build scripts"
title: "Batch and shell scripts"
description: "Batch scripts that run dcu32int over DCU sets per Delphi version"
---
# Scripts

- `all*.bat`, `alllib*.bat`, `run*.bat`: Windows batch runs of dcu32int over `*.dcu` sets for various Delphi versions (e.g. `alllibXE3w64D.bat`, `alllib9IL.bat` for .NET).
- `alllibK.sh`, `selftst.sh`: Kylix/Linux scripts; `selftst.sh` runs dcu32int over its own DCUs as a self-test.
- `DOSFTIME.RFI`, `UNIXTIME.RFI`: time-format resource descriptions.
- `dcu32int.dproj`/`.dpr`: Delphi project; the program source lists units from [modules](/overview.md).
