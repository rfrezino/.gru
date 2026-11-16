---
type: Component
title: Alert and HighTrafficAlert
description: Alert dataclasses with trigger/recovery messages.
resource: src/domain/monitor/alert.py
tags: [domain, alerting]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Alert holds triggered_at and optional recovered_at and message properties. HighTrafficAlert adds trigger_value (req/s) and messages such as 'High traffic generated an alert - hits = X, triggered at HH:mm:ss ZZZ'. Created by [AlertService](/components/alert-service.md).

