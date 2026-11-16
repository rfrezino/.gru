---
type: Mechanism
title: Tab filtering
description: How typing in the search edit shows or hides tab sheets.
tags: [delphi, vcl]
generated: { by: "claude-code/sonnet-5-5", at: "2026-11-16T00:00:00Z" }
resource: uRfPageControl.pas
---
# Tab filtering

- `TRfPageControlFilterComponents` creates the search page, edit and image; the edit's change handler calls a `TFilterProcedure` (the owner's `SetFilter`), then refocuses the edit.
- `TRfTabsFilter` (a `TList<TTabSheet>`) iterates tabs: the search tab is always visible; others stay visible if the filter is empty or the filter text appears in the lowercased caption (`Pos`). Hidden tabs get `TabVisible` and `Visible` false.
- Afterwards the first visible non-search tab becomes the active page, falling back to the search tab.
- `IsSearchItemVisibleOnNavigation` uses `TCM_HITTEST` at (5,5) to tell whether tab 0 is visible.

Owner: [TRfPageControl](/components/trfpagecontrol.md).
