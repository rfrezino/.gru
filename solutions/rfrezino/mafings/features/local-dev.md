---
type: Feature
title: Local development environment (spec 002)
description: Docker Compose Firebase emulators with seed fixtures, reset and persist modes, and guards against touching real projects.
tags: [feature, devex]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: pkg, resource: /package.json }
---

* `dev:local` seeds from `scripts/local/seed-data`; `dev:local:reset` wipes state; `dev:local:persist` keeps it; `test:local` runs all tests headlessly.[^pkg]
* Guard scripts abort unless the project ID starts with `demo-` and required ports are free.
* Stack overview: [architecture](/architecture/stack.md).

[^pkg]: package.json
