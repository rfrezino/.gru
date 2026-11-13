---
type: Decision
title: Read results over HTTP from the runner, through atenet-router
description: The sandbox serves its answer on port 80 and the backend reads it via atenet-router, replacing ax ssh and spec.debug for the API path.
tags: [decision, results, ax, router, runner]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
sources:
  - resource: images/task/runner/result.go
  - resource: api/internal/adapters/out/ax/router.go
  - resource: .claude/thoughts/investigations/2026-10-14-result-channel-decision.md
    title: Result channel decision record (options, evidence, GKE risks)
---

# Context

[ADR 003](003-result-via-workspace-file.md) reads `answer.txt` with `ax ssh`, which needs `spec.debug: true` (arbitrary exec in the sandbox). AX never reports that a command finished: no phase means "done" and the exit status is not read back.

# Decision

The [task image](/components/task-image.md) runs a Go runner that embeds the AX runner on an internal port and serves `GET /gru/v1/result` on port 80 itself (JSON: `state` pending|answer|error|exited, `text`, `truncated`, `exit_code`), proxying everything else (h2c included). [gru-api](/components/gru-api.md) reads it through the `atenet-router` Service with the header `ate-target-actor: default/<task>`. No `debug`, no sandbox egress, no new sandbox credentials. It supersedes ADR 003 for the API path only; the CLI's direct-AX path still uses `ax ssh` ([decision 007](007-both-cli-paths-and-own-sandbox-prefix.md)).

# Consequences

- `exit_code` is `null` for a moment after a result appears (the agent writes the file, then exits) and after a snapshot restore; never rely on it at that instant.
- The runner must survive a snapshot restore, because a router request resumes a suspended actor (checked by the e2e suite).
- `atenet-router` is unauthenticated and reaches every actor. The policies limit `gru-api`'s egress and `ax-server`'s ingress, but the router's own ingress is still open: any pod can read any task's result through it (open item in the [remaining plan](/plans/backend-service.md)).
- A router request resumes a suspended actor; the reconciler only polls live tasks and deletes after storing the result.
- Evidence and rejected options (agent callback, bucket, LLM gateway): the decision record in `.claude/thoughts/investigations/`.
