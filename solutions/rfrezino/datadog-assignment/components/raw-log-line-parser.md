---
type: Component
title: RawLogLineParser
description: Parses common log format lines into LogData.
resource: src/domain/log/raw_log_line_parser.py
tags: [domain, parsing]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Uses apachelogs COMMON format. The section is the first path segment of the request URI (e.g. /api/users -> /api); paths without a further slash that contain a dot map to /, otherwise the path itself. Returns [LogData](/components/log-data.md).

