---
type: "Module"
title: "Statistics report task"
description: "Builds and displays the periodic traffic report."
---
`must_display_report` is true when active and the display interval has elapsed. The report lists general info (lines analyzed, average per second,
bytes) and top tables for remote hosts, users, HTTP verbs, sections, HTTP versions and status codes, computed by `loglines/loglines_statistics.py`.
