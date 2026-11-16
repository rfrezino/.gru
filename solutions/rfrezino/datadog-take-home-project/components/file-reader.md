---
type: Source Module
title: File reader
tags: [log-monitor]
description: Reads lines appended since a seek position
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/file_watcher/file_reader.py

`read_new_lines_from_file(file_path, seek_start_position)` returns `FileReaderResult(new_lines, last_seek_position)`. Used by the [reader job](/components/w3c-log-file-reader-job.md); no handling of truncation or rotation.
