---
type: "Decision"
title: "Prompts are kept with the task and the run"
description: "A task's prompt and a run's prompt stay in MySQL after the work ends, and the single-item GET returns them to their owner, so the full picture (asked, asked back, answered) is visible."
---

## Context

A task's prompt was cleared once AX accepted it, and a run's prompt when the run ended; no API view carried it. A finished run showed its answer and its questions but not what had been asked, which made a run impossible to review or reproduce.

## Decision

- The prompt of a task (for a workflow stage, the composed prompt: request, previous answer, requested changes) and the prompt of a run are **kept**. Questions and answers were already kept (Decision 011).
- `GET /v1/tasks/{id}` and `GET /v1/workflow-runs/{id}` return `prompt`. Lists and the `POST` responses do not. Ownership is unchanged: another requester gets 404.
- The prompt is still never logged, never a metric label and never in the usage ledger. The UI shows it as plain text only.
- The stage **spec** (the skill, the system prompt) is still cleared once AX has it: it is the workflow's own text, available from the pinned workflow version.
- `PromptSHA256` stays for idempotent replays.

## Consequences

- Prompts sit at rest in MySQL (`tasks.prompt`, `workflow_runs.prompt`) for as long as the rows do, and may contain whatever a user typed. Access control is the existing per-requester ownership; they are removed by the sweep once `GRU_TEXT_RETENTION` (90 days by default) has passed since the work ended (decision 017); the row stays as history.
- Tasks and runs from before this decision have no prompt; the API omits the field and the UI says "Not recorded".
- The final answer is a separate store with its own limit (`GRU_RESULT_TTL`, 24h by default): it can expire while the prompt does not.
