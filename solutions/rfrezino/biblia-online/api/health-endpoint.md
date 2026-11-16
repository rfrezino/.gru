---
type: API Endpoint
title: GET /health
description: Health check returning a fixed payload.
tags: [api]
resource: backend/src/host/restapi/health.py
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

Returns `{"value": "Hello World"}` (`HealthResponse`). Implemented by `HealthRouter`, a `Routable` subclass using the `@get` decorator from `host/restapi/resources`. Mounted in main.py; see [layers](/architecture/layers.md).
