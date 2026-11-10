---
type: "Specification"
title: "Planned license API"
description: "Intended license generation/validation endpoints from README"
---
Per the README engineering guidelines (not yet implemented in [the backend](/backend.md)):

- `/api/generate-license` — triggered by a purchase webhook.
- `/api/validate-license?key=XXXX` — for CLI/registry validation.
- License validity 365 days; CLI example `inv download --image python:3.11 --key XXXX`.
- Images hosted privately (S3 or OCI registry) and served via pre-signed URLs or token auth.
