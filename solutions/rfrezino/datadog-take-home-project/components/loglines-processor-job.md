---
type: Source Module
title: LogLines processor job
tags: [log-monitor]
description: Consumer thread running the statistics and alert tasks
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/jobs/loglines_processor_job.py

`_ProcessLogLinesJob` (Thread) keeps a `MonitorStatus` (all log lines, report status, alert status). Each cycle it appends newly consumed lines, runs [report task](/components/task-report-statistics.md) when `must_display_report` is true, runs the [alert task](/components/task-alert-excessive-traffic.md) if active (analysis time = `datetime.now()`), then sleeps. Any exception terminates the thread silently (bare `except`). Lines accumulate forever in memory.
