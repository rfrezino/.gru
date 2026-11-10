---
type: "Project"
title: "SearchTabControl overview"
description: "Delphi VCL TPageControl descendant with a search box embedded in the tab strip."
---
# SearchTabControl

A Delphi VCL package (`pktRfPageControl.dpk`, requires Vcl, rtl, vclimg) containing the unit `uRfPageControl.pas`. It registers [TRfPageControl](/components/rf-page-control.md) on the "RfComponents" palette page. Set `ShowSearchFilter := True` and the first tab becomes a search box that filters tabs by caption. Based on a StackOverflow question about pinning a first tab on a TPageControl.

Other files: `ProjectTest.dpr`, `uMain.pas/.dfm` form a demo app with one TRfPageControl and ten tab sheets. License in `LICENSE`.
