---
type: Source Module
title: W3C parser
tags: [log-monitor]
description: Splits raw log lines into string fields
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/w3c_log/

`W3cLogLine` holds raw string fields. `parse_w3c_loglines_to_loglines(lines)` returns `W3cLogParserResult(success_linelogs, error_linelogs)`, catching any exception per line. Expected format is common log format: `host ident user [dd/Mon/yyyy:HH:MM:SS +zone] "VERB /path HTTP/x" status bytes`. Next stage: [mapper](/components/logline-mapper.md).
