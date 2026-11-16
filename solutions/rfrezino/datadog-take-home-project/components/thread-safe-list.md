---
type: Source Module
title: ThreadSafeList
tags: [log-monitor]
description: Lock-guarded list shared between jobs
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/thread_utils/thread_safe_list.py

Generic list with `append`, `extend`, `count` and `consume_current_list` (atomically returns contents and resets). Hand-off buffer between the reader and [processor](/components/loglines-processor-job.md) jobs.
