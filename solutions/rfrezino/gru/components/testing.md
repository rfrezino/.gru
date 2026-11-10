---
type: "Component"
title: "Test tiers and layout"
description: "The four-tier test structure every project follows, where each tier lives, and the make targets that run it."
---

# The rule

Every project, existing or planned (`cli`, `api`, `images/task` as `agent`, `providers`, `ui`, later `mobile`), splits its tests into the same four tiers and the same folders. Only e2e lives at the top, because it spans projects.

| Tier | Proves | Where | Runs with |
|---|---|---|---|
| unit | one piece of logic, in-process | `<project>/tests/unit` (plus white-box Go tests beside the code) | `make <project>-unit` |
| integration | a component with one real collaborator | `<project>/tests/integration` | `make <project>-integration` |
| acceptance | the shipped binary or image against its public contract | `<project>/tests/acceptance` | `make <project>-acceptance` |
| e2e | the whole stack on the dev cluster | `tests/e2e` | `make e2e` |

`make unit`, `integration`, `acceptance` and `e2e` run one tier across projects, `make check` runs everything that needs no cluster, Docker or image, and `RUN=<pattern>` narrows any target to one test.

# What exists

| | unit | integration | acceptance |
|---|---|---|---|
| [api](/components/gru-api.md) | `tests/unit/{app,httpapi,memory}`, white-box tests in `internal/` | MySQL contract suites (Docker) | the `gru-api --fake` binary over HTTP with token auth |
| [agent](/components/task-image.md) | settings, the provider port and registry, run flows with fakes | the real `gru-agent` binary and `any-llm-go` against fake provider servers | the shipped image under Docker |
| [ui](/components/ui.md) | types, generated API types vs `openapi.yaml`, components with a fake client (Vitest) | the app in jsdom against a real `gru-api --fake` | the built UI in Chromium against `gru-api --fake` (Playwright); e2e in `tests/e2e/ui` against the cluster |

e2e (`tests/e2e`): the AX adapter and reconciler on the live stack (`make api-e2e`), the deployed service (`make api-cluster-test`). It is a separate Go module kept at the import path `gru/api/e2e` so it may import the backend's `internal` packages.

# Why

- Go unit tests that use unexported symbols cannot leave their package, so those stay in place and `make <project>-unit` runs both sets; everything that can live in `tests/unit` does.
- Non-unit Go tests carry a build tag named after the tier, so a plain `go test ./...` never starts a container or a server.
- A tier that cannot run for lack of Docker or an image fails instead of skipping, so a green run always checked something.
- Acceptance and integration tests run the real artifact as a child process with a minimal environment and assert that tokens, keys and prompts are never printed or logged.

# Adding a project

Create the three folders, write at least one real test per tier that applies (a README saying why where one does not), add the three make targets, add them to the aggregates, and list the project in `tests/README.md` and `CLAUDE.md`.
