---
type: "Runbook"
title: "Local development"
description: "How to install, run and provision local infra for the backend."
---
Commands from `backend/Makefile`:
- `make install-init` installs uv; `make install` installs Python 3.13.
- `make run-dev`: uvicorn on port 9002 with reload.
- `make run-infra`: docker compose with DynamoDB Local (host port 9010) and dynamodb-admin (http://localhost:8001/tables/).
- `make lint`: ruff with fixes.
