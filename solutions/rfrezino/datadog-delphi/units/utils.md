---
type: Delphi Unit
title: delphiDatadog.utils
description: Formatting helpers for tags, priority, alert type and message escaping.
resource: delphiDatadog.utils.pas
tags: [delphi]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
- `DataTagsToText`: joins tags with commas and prefixes `|#`; empty array gives an empty string.
- `DataTagsEventPriorityToText`: low / normal.
- `DataTagsEventAlertToText`: error / warning / info / success; undefined gives empty.
- `EscapedMessage`: replaces newline text for message fields (the replace uses the literal two-character `\n`, not a real newline, so real newlines are likely not escaped).

Used by [event](/units/event.md), [service check](/units/service-check.md) and the [client](/units/statsdclient-impl.md). Types come from [header](/units/header.md).
