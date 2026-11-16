---
type: Runbook
title: Local development
description: How to run the backend and local DynamoDB.
tags: [ops]
resource: backend/Makefile
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

# Commands (from backend/)

- `make install-init` installs uv; `make install` installs Python 3.13.
- `make run-infra`: docker compose with DynamoDB Local (host port 9010) and dynamodb-admin (http://localhost:8001/tables/).
- `make run-dev`: uvicorn src.main:app on port 9002 with reload.
- `make lint`: ruff check with fixes.

Stack: Python >=3.11, FastAPI, lagom, boto3, structlog, moto, pytest. See [DynamoDB adapter](/adapters/dynamodb-day-context-repository.md).
