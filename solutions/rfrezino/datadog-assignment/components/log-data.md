---
type: Component
title: LogData
description: One parsed access log entry.
resource: src/domain/log/log_data.py
tags: [domain]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Dataclass: bytes_sent, final_status, remote_host, remote_user (optional), timestamp (epoch seconds), section. Produced by [RawLogLineParser](/components/raw-log-line-parser.md).

