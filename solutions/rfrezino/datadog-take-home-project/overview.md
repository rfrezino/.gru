---
type: Overview
title: HTTP Log Monitor overview
tags: [log-monitor]
description: What the application does and how it is structured
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# HTTP Log Monitor

A Python 3.8 console application (take-home project) that watches a W3C/common-format HTTP access log file, periodically prints traffic statistics and raises/recovers a high-traffic alert. Design follows a functional-programming style: frozen dataclasses, small pure functions, impure code isolated in thread jobs.

## Pipeline
1. [CLI entry point](/components/application.md) parses options and starts the [log monitor](/components/log-monitor.md).
2. [W3C log reader job](/components/w3c-log-file-reader-job.md) (producer thread) reads new lines using the [file reader](/components/file-reader.md), parses them with the [W3C parser](/components/w3c-parser.md), maps them via the [mapper](/components/logline-mapper.md) and pushes [LogLines](/components/loglines.md) into a [ThreadSafeList](/components/thread-safe-list.md).
3. [LogLines processor job](/components/loglines-processor-job.md) (consumer thread) drains the list and runs two tasks: [statistics report](/components/task-report-statistics.md) and [high traffic alert](/components/task-alert-excessive-traffic.md).

See also [running and testing](/guides/running-and-testing.md).

## Known limitations (from the README)
- Log history is never trimmed from memory.
- Little error handling for file reads; unhappy-path tests are missing.
