---
type: Source Module
title: W3C to LogLine mapper
tags: [log-monitor]
description: Converts parsed strings to typed LogLine
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/mapper/mapper_w3c_logline_to_logline.py

Converts datetime (`%d/%b/%Y:%H:%M:%S +%f`, so the timezone offset is parsed as microseconds), ints, and splits the request into verb, section (`/` + first path segment) and HTTP version, producing [LogLines](/components/loglines.md).
