---
type: "Runbook"
title: "Local development"
description: "Makefile targets to run backend and frontend"
---
- `make backend`: activates `../.venv` and runs `uvicorn main:app --reload` in `backend/`.
- `make frontend`: `npm install && npm run dev` in `frontend/`.
- `make dev`: both in parallel.

See [backend](/backend.md) and [frontend](/frontend.md).
