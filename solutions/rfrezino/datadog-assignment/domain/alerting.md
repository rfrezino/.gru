---
type: "Domain Model"
title: "Alerting and status"
description: "AlertService, Alert types, LogMonitorStatus and presenter port"
---

- `AlertService(repo, window, threshold)`: `get_alert()` merges buckets covering the window; if avg req/s >= threshold it creates a `HighTrafficAlert`, and sets `recovered_at` when traffic falls below.
- `Alert`/`HighTrafficAlert`: triggered_at, recovered_at, messages.
- `LogMonitorStatus`: ref_time, rates, response sizes, top sections/status/hosts/users, alert.
- `LogMonitorPresenter`: abstract output port.

Data comes from [the log model](/domain/log-model.md).
