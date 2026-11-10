---
type: "Component"
title: "gru CLI"
description: "Go command that runs a prompt as an agent task and prints the answer, either straight through AX or through gru-api."
---

> Superseded in part by [decision 021](/decisions/021-python-control-plane.md): the CLI was retired before the move and is not ported; this concept is history.

> **Retired 2026-10-28.** The `gru` CLI and its direct-AX path were removed (plan `retire-the-gru-cli`); callers use gru-api over HTTP or MCP. This page is history.

# Two paths, both supported

| | Direct AX (default) | API (`--api URL` or `$GRU_API_URL`) |
|---|---|---|
| Talks to | the `ax` binary and the cluster | [gru-api](/components/gru-api.md) over HTTP |
| Needs on this machine | `ax`, cluster access, task image, provider key | nothing but the URL and a token |
| Provider key | read from the shell or `.env`, sent in the Task spec | held by the server |
| Result read with | `ax ssh` ([decision 003](/decisions/003-result-via-workspace-file.md)) | the server, via the runner ([decision 006](/decisions/006-result-via-runner-http-and-router.md)) |
| Cleanup on Ctrl-C or timeout | deletes the task | cancels the task |

Neither replaces the other ([decision 007](/decisions/007-both-cli-paths-and-own-sandbox-prefix.md)).

# Usage

```bash
gru run [--backend <provider>] [--providers-file f] [--model name] [--timeout 3m] [--keep] [--verbose] "<prompt>"
gru run --api http://localhost:8080 [--api-token-file path] [--insecure-token] ...
make ask BACKEND=echo PROMPT="..."          # direct AX; sets GRU_TASK_IMAGE from .cache/task-image.ref
make ask-api BACKEND=echo PROMPT="..."      # through a running gru-api (uses .cache/api-token when present)
```

- `--backend` names a provider from the [catalog](/components/llm-providers.md) (shipped: `claude` default, `openai`, `echo`). On the direct path the CLI resolves it through the catalog (embedded, or `--providers-file` / `$GRU_PROVIDERS_FILE`): the catalog decides the adapter, endpoint, key variable and default model, and `--model` is required only where the entry says so (`openai`). With `--api` the server's catalog decides and the CLI sends the name as given, showing the server's refusal. `--backend echo` needs no key and exercises the whole path.
- With `--api` an agent may ask a question ([decision 011](/decisions/011-agents-ask-and-wait-suspended.md)): in a terminal `gru run` asks you and sends the answer; without one it leaves the task waiting, prints the command and exits 3. `gru inputs [task]` lists what waits for you or prints one run as a thread; `gru answer <task> <question> [answer|-]` answers. Questions are printed on single lines (they are model output). `gru chat [first message]` holds a conversation (`mode: chat`): replies on stdout, your messages on stdin, `/end` or end of input ends it. The direct path cannot take questions or conversations.
- Workflows (API only): `gru workflows list|get|create|validate|versions|archive` manage stored definitions (the file is capped at 128 KiB before sending and never echoed back; `validate` exits 2 when invalid). `gru run --workflow NAME (--one-shot | --interactive)` starts a run, prints the server's gate warnings, follows it and prints the final answer; the style has no default. Exit 0 succeeded, 3 waiting for an answer nobody here can give (the run keeps waiting; `gru answer --run RUN INPUT ANSWER` later), 4 failed, cancelled or timed out. `--out FILE` saves the work directory archive exactly as sent (mode 0600, never overwrites, never extracted). An interrupted one-shot run keeps running; an interactive one is cancelled only if you confirm. `gru inputs --run RUN` prints a run's thread (stages, verdicts, loops).
- Direct AX also takes `--image` and `--ax`; the provider key comes from `ANTHROPIC_API_KEY` / `OPENAI_API_KEY` or a gitignored `.env` that `make ask` sources (shell wins).

# Direct AX path

1. `internal/task` renders the Task YAML (`debug: true`, env `GRU_PROMPT`, `GRU_MODEL`, `GRU_BACKEND`, provider key).
2. `internal/axcli` wraps the `ax` binary (apply, resume, get, ssh, delete) with an injectable `Exec`.
3. New tasks are `Suspended`; `gru` resumes them, waits for `Running`, then polls `ax ssh` with a script whose exit code says answer (0), error (3) or pending (4).
4. The task is always deleted afterwards unless `--keep` (fresh context, so it survives timeouts, SIGINT and SIGTERM). A SIGKILL can leak a task ([runbook](/runbooks/troubleshooting.md)).
5. Errors are explained: `timed out after <d>`, `interrupted`, the agent's `error.txt`, `no free worker` (AX has no queue and marks the refused task `Failed`, so `gru` does not retry it); "golden tag not available" is retried until the deadline.

# API path

`internal/apiclient` (standard library only) submits with an `Idempotency-Key` (a retry can never create a second task), long-polls `GET /v1/tasks/{id}?wait=`, prints the answer, and warns if it was truncated. Transport errors, 5xx and 429 are retried; a 401 is not, and gets a hint naming both ways to supply a token. The token comes from `$GRU_API_TOKEN` or `--api-token-file`, never from a flag value, and the CLI refuses to send it in clear text to a non-local host without `--insecure-token` ([decision 008](/decisions/008-bearer-tokens-and-ownership.md)).

# Limits

- The direct path stores nothing and puts the provider key in the Task spec (AX env has no secret reference): local development only.
- The API path needs a reachable `gru-api` and, when auth is on, a token.

# Tests

`make cli-unit` (`go vet` and `go test`, with an `httptest` fake API for the API path), `make cli-integration` (the real `gru` binary against a stub `ax` executable) and `make cli-acceptance` (the binary against a stand-in API: flags, exit codes, messages). Narrow any of them with `RUN=`, for example `make cli-unit RUN=TestManifestQuotesPrompt`. See [testing](/components/testing.md).
