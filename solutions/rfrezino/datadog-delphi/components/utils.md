---
type: "Component"
title: "Utils"
description: "Helpers converting tags, priority, alert type and messages to StatsD text."
---
- `DataTagsToText`: comma-joined tags prefixed with `|#` (empty if none).
- `DataTagsEventPriorityToText`, `DataTagsEventAlertToText`: enum to wire text (`low`/`normal`; `error`/`warning`/`info`/`success`/empty).
- `EscapedMessage`: replaces newlines with an escaped form.

Types come from [header types](/components/header-types.md).
