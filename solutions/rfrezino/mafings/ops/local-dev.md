---
type: "Runbook"
title: "Local development environment"
description: "Docker Compose + Firebase emulators with committed seed data."
---
# Commands

| Command | Effect |
|---------|--------|
| `npm run dev:local` (`make dev-local`) | check project ID and ports, start emulators + import seed |
| `npm run dev:local:reset` | drop volumes, restart from seed |
| `npm run dev:local:persist` | export state on exit, reuse next start |
| `npm run test:local` | run tests under `firebase emulators:exec` |
| `npm run seed:bible` / `grant:admin` | seed Bible entity / grant admin claim |

Project ID is `demo-meusite-local`. Seed fixture in `scripts/local/seed-data` contains `entities/bible`, a sample daily message, and two admin users (local emulator only). Defined by [feature 002](/features/002-local-dev-environment.md).
