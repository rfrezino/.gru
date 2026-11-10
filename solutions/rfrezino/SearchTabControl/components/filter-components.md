---
type: "Delphi Class"
title: "TRfPageControlFilterComponents"
description: "Creates and manages the search tab sheet, edit box and icon."
---
# TRfPageControlFilterComponents

Owned by [TRfPageControl](/components/rf-page-control.md).

- Creates a blank-captioned TTabSheet (Tag = 57, `TAB_SEARCH`) at index 0 with a "No results found." panel.
- Creates a borderless TEdit on the tab strip; `OnChange` calls the filter callback, then refocuses the edit.
- Creates a magnifier TImage from an embedded hex-encoded PNG (TPngImage registered in `initialization`).
- `Visible` toggles everything; internal visibility toggles only edit and icon.
- `IsSearchItemVisibleOnNavigation` uses TCM_HITTEST at (5,5) to see whether tab 0 is in view.
