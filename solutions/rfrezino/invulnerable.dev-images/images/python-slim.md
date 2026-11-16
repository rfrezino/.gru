---
type: Container Image
title: Python slim images
description: Dockerfiles for python:3.11/3.12/3.13-slim bases with CVE fixes applied.
tags: [docker, python, security]
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: dockerfiles, resource: "python/slim/", title: "Dockerfile.3.11, 3.12, 3.13" }
---
# Overview

Repository `invulnerable.dev-images` holds hardened base images. Each file `python/slim/Dockerfile.<version>` starts from `python:<version>-slim` (stage `base`), sets `WORKDIR /app`, and adds label `org.invulnerable.image_layer_1='PUBLIC/python:<version>-slim'` recording the upstream image.

# Vulnerability fixes

| Version | Fix |
|---|---|
| 3.11 | Upgrade pip and setuptools>=78.1.1 (CVE-2024-6345, CVE-2025-47273, CVE-2022-40897) |
| 3.11, 3.12, 3.13 | Install libpcre2-8-0 via apt (CVE-2025-58050) |

The apt step uses `--no-install-recommends` and removes `/var/lib/apt/lists/*`.[^dockerfiles]

[^dockerfiles]: python/slim/Dockerfile.3.11, 3.12, 3.13
