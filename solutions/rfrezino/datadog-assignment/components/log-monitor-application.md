---
type: Component
title: LogMonitorApplication
description: Main loop that builds a status snapshot and presents it.
resource: src/application/log_monitor_application.py
tags: [application]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Every update_rate seconds (default 10) it fetches buckets since now - update_rate - 1 from the [repository](/components/log-data-bucket-repository.md), computes request rate, bytes per second and per request, top-N (default 5) sections, statuses, hosts and users, adds the current alert from [AlertService](/components/alert-service.md), and passes a [LogMonitorStatus](/components/log-monitor-status.md) to the [presenter](/components/log-monitor-presenter.md). Alert evaluation is tied to the refresh rate (IMPROVEMENTS.md).

