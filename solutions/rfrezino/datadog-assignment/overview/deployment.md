---
type: Deployment
title: Running and deployment
description: Docker, docker-compose, pipenv and load-test setup.
tags: [docker, ops]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
- docker-compose.yml defines the webserver (nginx, docker/webserver), the log-monitor image (docker/log-monitor) and a k6 load-test image (docker/load-test/test.js).
- Run `docker-compose up --build`, then `docker-compose run log-monitor log-monitor`, then `docker-compose run load-test k6 run test.js`; the default options trigger and recover an alert.
- Own log file: build docker/log-monitor/Dockerfile and mount the log read-only at /tmp/access.log.
- pipenv: Python 3.8 (walrus operator is used), `pipenv run log-monitor`.
- Dependencies: click, apachelogs, arrow, blessings, hurry.filesize, pyfiglet, tabulate.
- Tests live in test/ mirroring src layout.

CLI options are in [CLI](/components/cli-entrypoint.md).

