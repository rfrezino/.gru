---
type: Architecture Overview
title: gru architecture overview
description: How a prompt travels from a caller through gru-api, AX and Agent Substrate to Claude and back.
tags: [architecture, ax, substrate, gke]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
sources:
  - id: readme
    resource: README.md
    title: gru README
  - id: ax-runner
    resource: https://github.com/google/ax/blob/ac2332829f22360ff97b0ba34d94dd0dd782f17e/docs/runner.md
    title: AX runner contract
---

# Flow

```
caller (HTTP, MCP, UI) -> gru-api (MySQL) -> ax-server (gRPC) -> Agent Substrate -> gVisor actor
                                                              runner + gru-agent -> gateway (gru-api) -HTTPS-> Claude

result:  actor runner GET /gru/v1/result  <- atenet-router <- gru-api
```

1. A caller submits the prompt to [gru-api](/components/gru-api.md) over HTTP, MCP or the UI. (The `gru` CLI and its direct-AX path were retired on 2026-10-28; see [the old component](/components/gru-cli.md).)
2. [AX](/components/ax.md) schedules the task as an actor; [Agent Substrate](/components/agent-substrate.md) runs it in a gVisor sandbox on a pre-warmed worker.
3. The [task image](/components/task-image.md) runs `gru-agent`, which calls Claude through gru-api's gateway (with the organization's own key, which the sandbox never sees; [LLM providers](/components/llm-providers.md)) and writes `/workspace/answer.txt` (or `error.txt`); the image's runner serves that over HTTP.
4. The backend's reconciler reads the runner's result through `atenet-router`, stores it (kept `GRU_RESULT_TTL`), deletes the sandbox, and serves the answer on `GET /v1/tasks/{id}`.

# Why a file

AX does not report a task command's output or exit code to the caller.[^ax-runner] The durable `/workspace` volume is the only channel, so results travel through it. The first design read it with `ax ssh` ([decision 003](/decisions/003-result-via-workspace-file.md)); gru-api reads it over HTTP from the runner through the router ([decision 006](/decisions/006-result-via-runner-http-and-router.md)).

# Where it runs

Local development uses k3d plus Tilt ([dev environment](/components/dev-environment.md)); GKE is the target. `deploy/` holds the Kustomize manifests for AX and the worker pool, and `deploy/gru-api` runs the backend in the cluster with network policies ([deployment](/components/gru-api-deployment.md)). There is no GKE overlay yet.

# Trust boundaries

AX has no authentication, so [gru-api](/components/gru-api.md) is the boundary: callers authenticate with bearer tokens and see only their own tasks ([decision 008](/decisions/008-bearer-tokens-and-ownership.md)); network policies let only labelled clients reach it and only it reach AX. `atenet-router` is still open to every pod.

# What is left

OIDC, router isolation, a GKE overlay and database: see the [remaining plan](/plans/backend-service.md).

[^ax-runner]: AX runner contract
