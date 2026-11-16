---
type: Source Module
title: High traffic alert task
tags: [log-monitor]
description: Detects and recovers from excessive request rate
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/tasks/task_alert_excessive_traffic.py

- `get_alert_excessive_traffic_status`: takes lines within the last N minutes (window) ending at the analysis time, groups per epoch second ([loglines](/components/loglines.md)), and alerts if any second has more lines than the threshold.
- `display_alert_excessive_traffic_status`: logs only on state change: "High traffic generated an alert - hits = X, triggered at T" or "High traffic incident recovered at T".

Config: `TaskAlertHighTrafficConfiguration(active, maximum_loglines_per_seconds, logline_analysis_windows_in_minutes, logger)`. Invoked by the [processor job](/components/loglines-processor-job.md).
