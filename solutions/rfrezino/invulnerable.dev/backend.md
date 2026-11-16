---
type: Service
title: Backend (FastAPI)
tags: [invulnerable-dev]
description: Minimal FastAPI app with a hello endpoint and open CORS.
resource: backend/main.py
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
- `GET /api/hello` returns `{"message": "Hello from FastAPI!"}`.
- CORS allows all origins, methods and headers (local frontend dev).
- Runs via uvicorn on 127.0.0.1:8000 with reload; Python >=3.11, no dependencies declared in `pyproject.toml`.
- The license validation API described in the README is not implemented yet.

See [dev workflow](/dev-workflow.md).
