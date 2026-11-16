---
type: Project
title: SearchTabControl overview
description: Delphi VCL TPageControl descendant with a search box on a first tab that filters tabs.
tags: [delphi, vcl]
generated: { by: "claude-code/sonnet-5-5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: readme, resource: "README.md" }
---
# SearchTabControl

A Delphi VCL component package (`pktRfPageControl.dpk`, requires Vcl, rtl, vclimg) containing the unit `uRfPageControl.pas`. It registers `TRfPageControl` on the `RfComponents` palette. Based on a StackOverflow question about pinning a first tab on a TPageControl.[^readme]

`ProjectTest.dpr`, `uMain.pas` and `uMain.dfm` are a demo app: a form `TfrmMain` with a `TRfPageControl` and ten tab sheets.

See [TRfPageControl](/components/trfpagecontrol.md).

[^readme]: README.md
