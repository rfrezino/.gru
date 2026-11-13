---
type: Container Image
title: Python slim images
description: Dockerfiles for python 3.11, 3.12 and 3.13 slim base images with CVE fixes applied.
resource: python/slim/
tags: [docker, security]
generated: { by: "claude-code/sonnet-5-5", at: "2026-11-13T00:00:00Z" }
---
# Overview

Three Dockerfiles live in `python/slim/`: `Dockerfile.3.11`, `Dockerfile.3.12`, `Dockerfile.3.13`. They are identical except for the base tag (`python:<ver>-slim`) and the label.

- Base stage `base`, `WORKDIR /app`.
- Label `org.invulnerable.image_layer_1` records the public upstream image, e.g. `PUBLIC/python:3.12-slim`.
- A "Vulnerabilities fixes" section installs patched packages; currently `libpcre2-8-0` for CVE-2025-58050.

See [Vulnerability fix pattern](/vulnerability-fix-pattern.md).
