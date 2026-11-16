---
type: Decision
title: Temporal runs workflows; a fixed interpreter executes a compiled plan of the user's YAML
description: Users write simple YAML; the server compiles it to a plan and one fixed Temporal workflow interprets it, with stage tasks as activities, gates as signals and timers, and MySQL as the read model.
tags: [decision, workflows, temporal, engine]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-15T23:59:00Z }
sources:
  - resource: api/internal/domain/workflow/plan.go
  - resource: api/internal/domain/workflow/branch.go
  - resource: api/internal/app/runloop.go
  - resource: api/internal/app/runstage.go
  - resource: api/internal/adapters/out/temporal/workflow.go
  - resource: api/internal/adapters/out/temporal/engine.go
  - resource: .claude/thoughts/investigations/2026-10-15-temporal-run-engine.md
---

# Context

A workflow run is a few stages (agent, review, approval), a capped review loop and sometimes a human gate. The first engine was a hand-written reconciler over MySQL rows. It worked, but the next features (loops with caps, gates that survive restarts, timeouts that exclude waiting, cancellation that reaches a sandbox) are exactly what a durable-execution engine provides, and the owner chose Temporal.

# Decision

- **The YAML stays the contract.** Version 1 files and the validator, the canonical renderer, the graph and the board are unchanged. A superset (`timeout`, `retries`, a gate `timeout`) is planned as `version: 2`.
- **One fixed workflow interprets a compiled plan.** `workflow.Compile` turns a validated definition into a `Plan` (edges resolved to indexes, no skill text). The loop in `app.InterpretRun` applies the pure rules of package `workflow` (`Branch`, `AfterGate`) and does every effect through a small `RunEnv`. The Temporal workflow is a thin `RunEnv` (each call an activity, signal or timer); an in-process `LocalEngine` is another (tests and `--fake`, not durable). The rules and the loop exist once.
- **Stage tasks stay ordinary tasks.** An activity creates the stage's task (id derived from the run and the step, so a retry finds the same task), waits for it while heartbeating and returns small results. Questions, suspend and resume, the result store and `GET /v1/tasks/{id}` work as before. The workspace moves runner to store to runner as in [decision 012](/decisions/012-agents-work-on-files-in-a-confined-directory.md)'s transfer, never through Temporal.
- **One kind of stage, behaviour from options** (see [gru-api](/components/gru-api.md)). A stage has a skill and `on_success`/`on_fail`; `report_result`, `require_approval` and `ends` combine and are lowered to the interpreter's units, so the engine did not change except for a route for a stage that fails for good. Timeout and retries behave as before.
- **Gates are workflow-native.** An approval stage, or `on_max_loops: ask`, records a run-level question (`workflow_run_inputs`) and waits on the `answer` signal racing a durable timer (`GRU_INPUT_TIMEOUT`); an answer stored as the timer fires still counts. Cancel is workflow cancellation: the cleanup runs on a context the cancellation cannot reach.
- **MySQL is the read model.** Runs, steps and status events (`workflow_runs`, `workflow_run_steps`, `workflow_run_events`) serve list, filter, ownership and idempotency; activities write them idempotently. Temporal history is the execution record. The HTTP routes and `openapi.yaml` did not change.
- **History carries ids and small results only.** The workflow input is the run id; the request, the skills and every file are read from the stores by id. The one place model text enters history is a review's verdict (at most about 40 KiB), because the workflow must branch on it. Payload encryption is off for now; the design keeps it addable later.
- **Timeouts are explicit and simple.** The stage task's own timeout (which already excludes waiting for a person), a per-gate timeout and the stage-run ceiling (`GRU_RUN_MAX_STAGE_RUNS`) replace the single "run timeout excluding waiting".
- **A worker shutting down must not cancel a stage.** The activity gives its context a cancellation cause that tells a worker stop from a workflow cancel; a stop leaves the task running and another worker finds it. A cancel reaches the activity on a heartbeat, so the heartbeat timeout (5 s) is the worst-case cancel delay.
- **Front ends are thin.** The use cases are in `app` and know no transport; the engine is the `app.RunEngine` port. The HTTP adapter is one way in; an MCP adapter will be another through the same use cases. A layering test keeps the SDK out of `domain`, `app` and inbound adapters.

# Operation

- `gru-api` serves HTTP and starts, cancels and signals runs; `gru-api --worker --no-http` (a separate Deployment, the same image) executes them. `GRU_TEMPORAL_ADDR`, `GRU_TEMPORAL_NAMESPACE`, `GRU_TEMPORAL_TASK_QUEUE`. An outbox pass restarts runs that were stored but never started.
- Dev: the open-source dev server (pinned) in the `temporal` namespace, SQLite on an emptyDir, reachable only from `gru-api` and the worker. Production: a Temporal server on a relational database of its own (MySQL 8 or PostgreSQL; a Cloud SQL instance on GKE, separate from the application's).
- Tests: the interpreter in the SDK's in-process environment, replay of recorded histories (`internal/adapters/out/temporal/testdata`; change the interpreter with `workflow.GetVersion`), and `make api-temporal-test` against a throwaway dev server in Docker (durability across a worker restart, signals, timers, cancel, history contents).

# Consequences

- A Temporal server and its database are now part of the platform; the dev server is not durable, so a dev restart loses in-flight runs.
- Changing the interpreter needs version discipline (`GetVersion` and a new recorded history) so runs in flight keep working.
- The `LocalEngine` is not durable: it is for tests and `--fake` only.
