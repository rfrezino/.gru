---
type: Component
title: LogDataBucket
description: Aggregated counters for a time span of log data.
resource: src/domain/log/log_data_bucket.py
tags: [domain]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Holds timestamp, timespan, requests, total_bytes_sent and count dicts for status, host, user, section. add_log_data counts one [LogData](/components/log-data.md); in-place addition (+=) merges buckets and widens the span.

