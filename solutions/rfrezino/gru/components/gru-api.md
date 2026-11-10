---
type: "Component"
title: "gru-api (backend service)"
description: "Go service that accepts tasks over REST, persists them in MySQL, drives them through AX and returns the answer."
---

# What it does

`POST /v1/tasks` stores a `Pending` task and returns 202; a reconciler starts it on AX, watches it, stores the result and deletes the sandbox; `GET /v1/tasks/{id}` (optionally `?wait=30s`) returns the status and, for a `Succeeded` task, the `answer` until it expires. List and cancel exist too. The row is always written before AX is called. The full endpoint table, data model and decisions are in the spec.

# Layout (onion)

`internal/domain` (no I/O) <- `internal/app` (use cases and ports) <- `internal/adapters/{in/http, out/store, out/ax, out/memory}`; `cmd/gru-api` is the composition root. `internal/archtest` fails the build if `domain` or `app` import an adapter, `net/http`, `database/sql` or a driver. Every `TaskRepository`, `ResultStore` and `AgentRuntime` must pass the shared contract suites (`repotest`, `resulttest`, `runtimetest`), so the in-memory fakes and the real adapters stay equivalent.

# The run engine

Workflow runs (`/v1/workflow-runs`) are executed by Temporal behind the `app.RunEngine` port ([decision 014](/decisions/014-temporal-runs-the-workflows.md)): `StartRun` stores the run and starts a workflow whose id is the run id; an outbox pass starts runs whose start was lost. `gru-api --worker` runs the interpreter and its activities (the stage use cases in `app/runstage.go` and `app/rungate.go`); `--fake` swaps in an in-process engine so no server is needed. The use cases (start, get, list, cancel, answer, timeline, workspace, `WaitForRun`) are transport-agnostic so an MCP front end can reuse them.

**One kind of stage.** Every stage has a skill, `on_success` and `on_fail`, and options switch on what it does: `report_result` (the agent reports success, or failure with the changes it wants), `require_approval: "<question>"` (a person approves or rejects once the agent is done; without a skill it is a pure gate), `ends: success|fail` (a stage that ends the run), `max_loops` and `on_max_loops`, `timeout` and `retries`. Options combine. `workflow.Definition.Lower` turns a stage into the engine's own units (agent, review, approval, succeed, fail); a stage with an agent part and an approval is two units, the second named `<id>/approval`. A stage that fails for good (after its retries) goes along `on_fail` when it has one, and fails the run otherwise. A stage `timeout` becomes its task's timeout (`tasks.timeout_ms`, default `GRU_TASK_TIMEOUT`), or how long a person has on a pure gate (default `GRU_INPUT_TIMEOUT`). `retries` runs a failed agent part again as a new step of the same visit; every attempt counts toward `GRU_RUN_MAX_STAGE_RUNS`. The graph carries the options and two edge kinds (`success`, `fail`), so the board loses nothing on a round trip. There is one schema version: the old five-type YAML was dropped in the dev phase, not migrated.

# The reconciler

`Drive` claims due tasks with a database lease (`FOR UPDATE SKIP LOCKED`), so any number of replicas can run it. For each task:

1. **Pending:** `Start` creates the AX task (tolerating one that exists) and resumes it. No capacity or a transient error keeps the task `Pending` with backoff and never fails it; only a permanent error does. On success the row moves to `Submitted` with the AX name and the prompt cleared.
2. **Observe:** AX phase says only whether the sandbox is up; the outcome comes from the runner's result, read through `atenet-router` ([decision 006](/decisions/006-result-via-runner-http-and-router.md)). AX phase `Failed` is never read as an agent failure.
3. **Terminal:** store the result, then the status, then delete the sandbox, so a crash at any point is recoverable. A finished answer wins over the task timeout (`GRU_TASK_TIMEOUT`, counted from `submitted_at`).
4. **Sweep** (`GRU_SWEEP_INTERVAL`): deletes sandboxes under its own name prefix that have no live task after `GRU_ORPHAN_GRACE`, and expired results. See [decision 007](/decisions/007-both-cli-paths-and-own-sandbox-prefix.md).

# Questions and waiting

An agent can ask the user ([decision 011](/decisions/011-agents-ask-and-wait-suspended.md)). The reconciler stores the question (`task_inputs`), moves the task to `AwaitingInput` and suspends the sandbox; `POST /v1/tasks/{id}/inputs/{input}/answer` stores the answer and wakes the task, which resumes the sandbox, delivers the answer and runs again. `GET /v1/tasks/{id}` and the list show `pending_input`; `GET /v1/tasks/{id}/events` merges the status history with the questions and answers into one thread; `GET /v1/providers` lists what a task may name. Waiting does not count toward `GRU_TASK_TIMEOUT`; `GRU_INPUT_TIMEOUT` (24h) ends an unanswered question and `GRU_INPUT_POLL_INTERVAL` (10s) is how often a waiting task is looked at. `api/openapi.yaml` documents every route; a unit test fails when it and the handler disagree. `gru-api --fake` plays an asking agent for `ASK:` prompts and a conversation for `mode: chat`. A task with `mode: chat` is a conversation: each reply is a question of kind `chat`, the answer is the next message, `/end` ends it ([decision 011](/decisions/011-agents-ask-and-wait-suspended.md)).

# Authentication and ownership

Bearer tokens (`GRU_AUTH_MODE=token`) behind an `Authenticator` seam; every task belongs to its requester and others get 404 ([decision 008](/decisions/008-bearer-tokens-and-ownership.md)). `GRU_AUTH_MODE=none` is for local development.

# Running it

| How | Command |
|---|---|
| Fake runtime, in memory | `cd api && go run ./cmd/gru-api --fake` |
| Real AX and MySQL on your machine | `make api-dev` (port-forwards AX, the router and the dev MySQL) |
| Inside the cluster | `make api-image api-up`; see [deployment](/components/gru-api-deployment.md) |
| Schema only | `gru-api --migrate-only` |

Configuration is environment only (`GRU_*`; the list is in the spec). Without `--fake` it needs `GRU_MYSQL_DSN`, `GRU_TASK_IMAGE`, `GRU_AX_ADDR` and `GRU_ROUTER_URL`. Which LLM providers a task may name comes from the [provider catalog](/components/llm-providers.md) (`GRU_PROVIDERS_FILE`, else the embedded default; a catalog that does not validate stops the service): `POST /v1/tasks` takes a catalog name and a model, never a URL or key variable, and the AX adapter builds the Task env (`GRU_ADAPTER`, `GRU_BASE_URL`, `GRU_KEY_ENV`, `GRU_MODEL`) from the resolved provider. Dev only: the backend reads the provider keys from its own environment and puts them in the AX Task env as plain text.

# Observability

JSON logs (`GRU_LOG_FORMAT=json`) that never contain prompts, answers, tokens or keys. `GRU_METRICS_ADDR` opens a separate listener with Prometheus text: requests by route and status, auth failures, reconciler and sweep errors, what the sweep removed, tasks by status, `gru_store_up`.

# Tests

`make api-unit` (vet, unit tests, layering), `make api-acceptance` (the `gru-api --fake` binary over HTTP), `make api-integration` (MySQL suites in a throwaway container; `api-check` runs the first two), `make api-e2e` (the real AX adapter and reconciler on the dev cluster), `make api-cluster-test` (the deployed service). The failure tests in `internal/app/failure_test.go` map one to one to the error matrix in the spec.

# Not done yet

OIDC, provider-key delivery for GKE, a GKE overlay and database, observability wiring: see the [remaining plan](/plans/backend-service.md).

Related: [gru CLI](/components/gru-cli.md) (its `--api` mode), [AX](/components/ax.md), [task image](/components/task-image.md).
