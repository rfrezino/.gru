---
type: Source Module
title: Report statistics task
tags: [log-monitor]
description: Builds and logs the periodic traffic report
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/tasks/task_report_statistics.py

`must_display_report` checks activity and elapsed seconds since last display; `generate_report_output` renders text sections (top hosts, users, verbs, sections, HTTP versions, status codes) from [statistics](/components/loglines-statistics.md); `display_report` logs it. Known bug: the "Logs from ... to" line prints the literal text `report_statistics.newer_log_line_datetime` (missing f-string braces).
