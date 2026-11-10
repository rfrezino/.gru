---
type: "Container Image"
title: "Task image (images/task)"
description: "Sandbox image with the gru runner (which serves the result over HTTP) and gru-agent, the Go agent that calls the configured LLM provider."
---

> Superseded in part by [decision 021](/decisions/021-python-control-plane.md): the sandbox agent is Python (`images/task/agent/gru_agent`) and the image has a slim Python base; the runner is still Go and unchanged.

# Contents

- **The gru runner**, installed at `/usr/local/bin/ax-task-runner` (AX requires an executable there as PID 1; the default `gcr.io/ax-substrate/...` image is not publicly pullable). It embeds `github.com/google/ax/runner` on an internal port (8081) and serves port 80 itself: `GET /gru/v1/result` locally and `/healthz`, everything else proxied to the embedded runner, h2c included so the CLI's `ax ssh` keeps working.[^decision] The result is `{state: pending|answer|error|exited, text, truncated, exit_code}`, read from `/workspace/answer.txt` or `error.txt`, capped at 1 MiB.
- `gru-agent` at `/usr/local/bin/gru-agent`: a static Go binary (`images/task/agent`, built by `make task-image`). It asks the configured provider through the `llm.Provider` port, implemented with Mozilla.ai's `any-llm-go` (pinned) for Anthropic, OpenAI, DeepSeek, Gemini, Groq, Mistral and Ollama, plus a generic `openai-compat` adapter and `echo`; see [LLM providers](/components/llm-providers.md). The base is `debian:bookworm-slim` with CA certificates, curl, git, procps and bash; no Python.

# Agent contract

| Env | Meaning |
|---|---|
| `GRU_PROMPT` | the prompt (required) |
| `GRU_ADAPTER` | `anthropic`, `openai`, `deepseek`, `gemini`, `groq`, `mistral`, `ollama`, `openai-compat` or `echo`; set by the API or CLI from the provider catalog |
| `GRU_BASE_URL` | endpoint (required for `openai-compat`, optional otherwise); https, or http only for localhost and in-cluster hosts |
| `GRU_KEY_ENV` | name of the variable holding the credential (`VENDOR_API_KEY` or `_TOKEN`, never `GRU_`) |
| `GRU_MODEL`, `GRU_MAX_TOKENS` (1024), `GRU_HTTP_TIMEOUT` (120 s per model call) | model and limits |
| `GRU_BACKEND` | the catalog name, for messages; tasks that set only this keep working through the legacy names `claude`, `openai`, `echo` |

A workflow stage adds optional variables (unset keeps the plain task behaviour; a bad value is a configuration error written to `error.txt`):

| Env | Meaning |
|---|---|
| `GRU_SYSTEM` | the stage skill, sent as the system message (up to 16 KiB) |
| `GRU_STAGE_TYPE` | `agent` or `review`; `review` needs `GRU_FILE_TOOLS=1` |
| `GRU_FILE_TOOLS` | `1` offers the file tools, rooted at `/workspace/work` |
| `GRU_INTERACTIVE` | `0` removes `ask_user` (a one-shot run); unset or `1` keeps it |
| `GRU_IMPORT`, `GRU_IMPORT_TIMEOUT` | `1` makes the agent wait for `/workspace/.gru-import-complete` (up to the timeout, 1 to 3600 s, default 300) before it starts; if it never appears the run fails with "workspace import did not arrive in time" |
| `GRU_MAX_TURNS` | model calls in a run, 1 to 100 (default 12) |

The agent re-validates the base URL and the key variable name with the catalog's rules, so a tampered Task env cannot redirect prompts or name an arbitrary variable. The provider key is removed from any error text before it reaches `error.txt`.

Output: `/workspace/answer.txt` (written via temp file and rename, so readers never see partial content), or `/workspace/error.txt` with a non-zero exit.

# Stage tools

A stage's agent can read, list, search, write and edit text files under `/workspace/work` and, in a review stage, submit a verdict, all through narrow tools confined with `os.Root` ([decision 012](/decisions/012-agents-work-on-files-in-a-confined-directory.md)). The verdict goes to `/workspace/verdict.json`; a review that ends without one fails ("review finished without a verdict"). The `echo` provider scripts the tools with prompt lines `WRITE <path>: <text>`, `EDIT <path>: <old> => <new>`, `READ <path>`, `LIST <path>`, `VERDICT approve: <summary>` and `VERDICT changes: <summary> | <change> | ...`.

# Workspace transfer

The runner moves a stage's work directory between sandboxes through `gru-api`, with no shared storage and no network from the sandbox. `GET /gru/v1/workspace` returns a tar.gz of `/workspace/work` with its sha256 in `X-Gru-Archive-Sha256` (413 over the limits, 422 for a symlink or other special file, 409 while an import runs). `PUT /gru/v1/workspace` takes a tar.gz of at most 16 MiB, unpacks it into a temporary directory, swaps it into `work/` and writes `/workspace/.gru-import-complete` (the sha256) last; the same archive again is 200, a different one 409, an unsafe one 422 with a fixed reason that never names an entry. The unpacker accepts only plain files and directories in clean form, refuses `..`, absolute names, links, devices and reserved `.gru*` names, and bounds entries (2000), entry size (1 MiB) and total content (16 MiB, counted while reading so a compression bomb stops early). `GET /gru/v1/result` also carries the review `verdict` from `/workspace/verdict.json` when it is valid. Like the other runner endpoints these have no authentication: the NetworkPolicy on the router is the protection, and nothing logs a body or an entry name. Hardening the image itself is a proposed spike ([decision 013](/decisions/013-sandbox-hardening-outcome.md)).

# Build

The runner must be compiled inside the pinned AX module (250 modules; it cannot be a dependency of `api/`), so `dev/scripts/build-runner.sh` builds it with `go build -overlay`, mapping `images/task/runner` into `.cache/ax/cmd/gru-task-runner/` without writing into the checkout. `make task-image` builds the runner and `gru-agent`, then the image, pushes to `localhost:5001/gru-task:dev` and records the digest in `.cache/task-image.ref`; tasks reference the digest. `make runner-test` runs the runner's unit tests the same way.

# Questions

The agent can ask the user something and wait ([decision 011](/decisions/011-agents-ask-and-wait-suspended.md)): it writes `/workspace/input-request.json` and waits for `/workspace/input-response.json`. The runner reports the pending question in `GET /gru/v1/result` (state `awaiting_input`) and accepts the answer on `POST /gru/v1/input` (idempotent; 409 for another question or a different answer). Suspending the sandbox keeps `/workspace` and restarts the agent, so it keeps `/workspace/transcript.json` and answers an already-answered question from it. The `echo` provider asks on prompt lines `ASK: ...`, `ASK-CHOICE a|b: ...`, `ASK-APPROVAL: ...` so this can be tested without a key.

[^decision]: Result channel decision
