---
type: "Delphi Component"
title: "TRfPageControl"
description: "TPageControl subclass exposing ShowSearchFilter and Filter."
---
# TRfPageControl

- Published: `ShowSearchFilter: Boolean`. Public: `Filter: String` (setter lowercases/trims, builds a [TRfTabsFilter](/components/tabs-filter.md), applies it, then calls Change), `GetFirstCaption`.
- `Loaded` (runtime only) creates [filter components](/components/filter-components.md) when enabled and activates page 1 (page 0 is the search tab).
- `Change` focuses the parent form's ActiveControl on the control and bounces from tab 0 to tab 1 when the filter is on.
- `MouseActivate` detects clicks on the navigator next/prior buttons (hard-coded 16px rects at the right edge) to hide/show the search edit when tabs are scrolled.
- Nothing is created at design time.
