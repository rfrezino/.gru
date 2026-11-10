---
type: "Service"
title: "Backend API"
description: "Minimal FastAPI app with one hello endpoint"
---
FastAPI app with CORS allowing all origins, methods and headers (local dev convenience). Only endpoint: `GET /api/hello` returns `{"message": "Hello from FastAPI!"}`. Runs on 127.0.0.1:8000 with reload. Python >=3.11, no declared dependencies in `pyproject.toml`. The license endpoints in [license-api](/license-api.md) are not implemented.
