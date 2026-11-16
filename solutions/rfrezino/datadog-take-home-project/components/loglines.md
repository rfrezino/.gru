---
type: Source Module
title: LogLine model
tags: [log-monitor]
description: Domain dataclasses and time helpers
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/loglines/loglines.py

Frozen dataclasses `RequestBody(verb, section, full_request_url, http_version)`, `LogLine(remote_host, remote_log_name, authenticated_user_name, request_datetime, request_body, return_http_status_code, document_transferred_bytes)` and `LogLineGroupPerEpochTime`. Helpers: `group_loglines_per_epoch_time`, `get_line_logs_in_period` (inclusive range filter; its `sorted` call result is discarded), `remove_old_log_entries` (unused by the job).
