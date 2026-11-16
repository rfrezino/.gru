---
type: Delphi Component
title: TRfPageControl
description: TPageControl subclass that adds a pinned search tab to filter tabs by caption.
tags: [delphi, vcl]
generated: { by: "claude-code/sonnet-5-5", at: "2026-11-16T00:00:00Z" }
resource: uRfPageControl.pas
---
# TRfPageControl

Descends from `TPageControl`.

- Published property `ShowSearchFilter: Boolean`; setting true creates the search UI (an edit, an image and a search tab) via an internal filter-components object.
- Public `Filter: String` (virtual `SetFilter`) and `GetFirstCaption`.
- `Change` sets the parent form's ActiveControl to the control and, at runtime with search enabled, moves selection off tab 0 (the search tab) to tab 1.
- `MouseActivate` and navigator button hit tests keep the search tab visible when tabs scroll.
- The search tab is identified by `Tag = 57` (`TAB_SEARCH`).

Filtering logic: [Tab filtering](/components/tab-filtering.md).
