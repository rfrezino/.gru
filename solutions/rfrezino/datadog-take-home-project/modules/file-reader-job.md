---
type: "Module"
title: "W3C log file reader job"
description: "Thread that reads new lines from the log file and parses them."
---
Periodically calls `read_new_lines_from_file` (src/file_watcher/file_reader.py), which seeks to the last position and returns new lines
plus the new position. Lines go through the [parser](/modules/w3c-parser.md) and `mapper_w3c_logline_to_logline` into the shared list.
