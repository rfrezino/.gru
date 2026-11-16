---
type: Component
title: Build and packaging
description: Packages per Delphi version, installer, Delphinus
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Packages
---

`Packages/DelphiSeattle|DelphiBerlin|DelphiTokyo` each hold `RFindUnit.dpk/.dproj`. `Installer/` is a Delphi installer app (`uInstaller`, `uDelphiInstallationCheck`). `Delphinus.*.json` enable install through the Delphinus package manager. `catalog-info.yaml` is a service catalog entry.
