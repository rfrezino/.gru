---
type: Decision
title: Both CLI paths stay, and the API owns the gru-api- sandbox prefix
description: gru run keeps its direct-AX path next to the API path, so the API's orphan sweep may only touch sandboxes under its own prefix.
tags: [decision, cli, api, sweep, ax]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
sources:
  - resource: api/internal/adapters/out/ax/taskspec.go
  - resource: api/internal/app/sweep.go
  - resource: tests/e2e/service_test.go
---

> **Retired 2026-10-28.** The `gru` CLI and its direct-AX path were removed (plan `retire-the-gru-cli`); callers use gru-api over HTTP or MCP. This page is history.

# Context

The plan was to switch the CLI from AX to the API. The owner decided to keep both: `gru run` still talks to AX through the `ax` binary by default, and `gru run --api URL` uses the [API](/components/gru-api.md). The two paths name their AX tasks differently: the CLI uses `gru-<date>-<time>-<id>`; the API used `gru-<task id>`.

The API's sweep deletes every sandbox it lists that has no live task after a grace period. Listing by the bare `gru-` prefix made it delete CLI tasks. Measured on the dev cluster: a `gru run --keep` task was removed 152 seconds after creation, so any direct-AX run longer than the 2-minute grace period (the CLI's own timeout is 3 minutes) would have been killed.

# Decision

- Keep both CLI paths; never remove `axcli` or `debug: true` while the direct path is supported.
- API sandboxes are named `gru-api-<id>` (`GRU_SANDBOX_PREFIX`, validated), the AX runtime's `List` returns only that prefix and the task id behind each name, and the sweep never sees anything else.
- Tests that run a real sweep use a prefix of their own (`gru-e2e-`), so they cannot sweep a gru-api running in the same cluster.

# Consequences

- The prefix must be unique per deployment sharing an AX.
- A CLI task that survives a hard kill is not cleaned up automatically; the [troubleshooting runbook](/runbooks/troubleshooting.md) says how to remove it.
- Verified: a CLI-style task survived 215 seconds with `gru-api` running, and a real-AX sweep test checks that a CLI-style name is left alone.
