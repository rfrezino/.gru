---
type: Component
title: LogMonitorStatus
description: Snapshot dataclass handed to the presenter.
resource: src/domain/monitor/log_monitor_status.py
tags: [domain]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Fields: ref_time, timespan, request_rate, response_size_per_sec, response_size_per_req, top_section, top_final_status, top_remote_host, top_remote_user (lists of (value, count)), and optional [alert](/components/alert.md).

