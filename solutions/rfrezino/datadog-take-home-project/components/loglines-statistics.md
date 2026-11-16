---
type: Source Module
title: LogLines statistics
tags: [log-monitor]
description: Counters and aggregates over log lines
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/loglines/loglines_statistics.py

`generate_report_statistics(log_lines, logger)` returns `LogLinesStatistics`: per-field counts sorted descending (host, user, verb, section, HTTP version, status), total bytes, line count, first/last datetime and average lines per second (count divided by seconds between first and last line; the count itself if 0 seconds). Assumes non-empty input and chronological order. Used by [report task](/components/task-report-statistics.md).
