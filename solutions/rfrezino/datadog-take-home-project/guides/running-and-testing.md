---
type: Guide
title: Running and testing
tags: [log-monitor]
description: How to run, build with Docker and test
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# Run locally
`pipenv install`, then `python src/application.py run start --w3c-logfile-path /path/log_file.log`. Type `exit` to quit.

# Docker
`make clean`, `make build`, `make run logtimezone=Europe/Amsterdam logdir=/path/ logfile=test.log`. The image is `python:3.8` running `application.py run`; the timezone is passed as `TZ` since alerts compare against local time.

# Tests (unittest)
`make run_unit_tests`, `make run_integration_tests`, `make run_acceptance_tests` (under tests/unit_tests, integration_test, acceptance_test). `make run_code_style_checks` runs flake8.

See [overview](/overview.md).
