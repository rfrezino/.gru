---
type: "Delphi Class"
title: "TRfTabsFilter"
description: "List of tab sheets that applies a text filter to tab visibility."
---
# TRfTabsFilter

A `TList<TTabSheet>` created by [TRfPageControl](/components/rf-page-control.md).

- `Filter(text)`: sets TabVisible and Visible per tab. A tab matches if it is the search tab (Tag 57), the filter is empty, or the lowercase caption contains the filter.
- Then activates the first visible non-search tab, falling back to the search tab (showing "No results found.").
