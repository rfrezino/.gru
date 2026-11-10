---
type: "Architecture Overview"
title: "gru architecture overview"
description: "How a prompt travels from the gru CLI through AX and Agent Substrate to an LLM and back."
---

# Flow

```
caller (HTTP, MCP, UI) -> gru-api (MySQL) -> ax-server (gRPC) -> Agent Substrate -> gVisor actor
                                                              runner + gru-agent -HTTPS-> LLM provider

result:  actor runner GET /gru/v1/result  <- atenet-router <- gru-api
```

1. A caller submits the prompt to [gru-api](/components/gru-api.md) over HTTP, MCP or the UI. (The `gru` CLI and its direct-AX path were retired on 2026-10-28; see [the old component](/components/gru-cli.md).)
2. [AX](/components/ax.md) schedules the task as an actor; [Agent Substrate](/components/agent-substrate.md) runs it in a gVisor sandbox on a pre-warmed worker.
3. The [task image](/components/task-image.md) runs `gru-agent`, which calls the provider and writes `/workspace/answer.txt` (or `error.txt`); the image's runner serves that over HTTP.
4. The backend's reconciler reads the runner's result through `atenet-router`, stores it (kept `GRU_RESULT_TTL`), deletes the sandbox, and serves the answer on `GET /v1/tasks/{id}`.

# Why a file

AX does not report a task command's output or exit code to the caller.[^ax-runner] The durable `/workspace` volume is the only channel, so results travel through it. The first design read it with `ax ssh` ([decision 003](/decisions/003-result-via-workspace-file.md)); gru-api reads it over HTTP from the runner through the router ([decision 006](/decisions/006-result-via-runner-http-and-router.md)).

# Where it runs

Local development uses k3d plus Tilt ([dev environment](/components/dev-environment.md)); GKE is the target. `deploy/` holds the Kustomize manifests for AX and the worker pool, and `deploy/gru-api` runs the backend in the cluster with network policies ([deployment](/components/gru-api-deployment.md)). There is no GKE overlay yet.

# Trust boundaries

AX has no authentication, so [gru-api](/components/gru-api.md) is the boundary: callers authenticate with bearer tokens and see only their own tasks ([decision 008](/decisions/008-bearer-tokens-and-ownership.md)); network policies let only labelled clients reach it and only it reach AX. `atenet-router` is still open to every pod.

# What is left

OIDC, provider-key delivery for GKE, router isolation, a GKE overlay and database: see the [remaining plan](/plans/backend-service.md).

[^ax-runner]: AX runner contract
