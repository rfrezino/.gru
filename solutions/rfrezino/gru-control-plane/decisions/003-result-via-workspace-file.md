---
type: "Decision"
title: "Return results through a file in /workspace"
description: "The agent writes answer.txt and the CLI reads it with ax ssh on the direct-AX path, because AX returns no task output."
---

# Context

The control plane does not read a task command's exit status or output.[^runner] The supported read-back paths are `ax ssh` (needs `spec.debug: true`) and the runner's HTTP endpoints.

# Decision

`gru-agent` writes `/workspace/answer.txt` or `error.txt` atomically; `gru` polls with `ax ssh` and a script whose exit code distinguishes answer (0), error (3) and pending (4); `ax ssh` passes remote exit codes through.

Scope: this is still how the CLI's **direct-AX path** reads results. The API path uses [decision 006](006-result-via-runner-http-and-router.md), which serves the same files over HTTP from the runner (the files themselves, `answer.txt` and `error.txt`, are unchanged).

# Consequences and alternatives

- `debug: true` exposes arbitrary exec in the sandbox: acceptable locally, not for production.
- Results vanish with the task; nothing is stored.
- Alternatives considered for the backend (agent callback with a per-run token, a shared bucket, an LLM gateway) were evaluated and rejected in favour of 006.

[^runner]: AX runner contract
