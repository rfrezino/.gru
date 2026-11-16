---
type: Runbook
title: Development workflow
tags: [invulnerable-dev]
description: Makefile targets to run backend and frontend.
resource: Makefile
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
- `make backend` — activates `../.venv` and runs `uvicorn main:app --reload`.
- `make frontend` — `npm install && npm run dev`.
- `make dev` — both in parallel.
