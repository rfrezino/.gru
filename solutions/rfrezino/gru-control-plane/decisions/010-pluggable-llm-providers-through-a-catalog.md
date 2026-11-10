---
type: "Decision"
title: "Pluggable LLM providers through a catalog and a port, in Go"
description: "Vendors are entries in an operator-controlled catalog read by the API, the CLI and the agent; the agent is Go behind an LLM port implemented with any-llm-go."
---

> Superseded in part by [decision 021](021-python-control-plane.md): the backend and the sandbox agent are Python now (the agent keeps the `Provider` port and the catalog, with the `anthropic` and `openai` SDKs instead of `any-llm-go`, so Python is back in the task image); "everything is Go" no longer holds. The catalog rules, the allowlist and the fail-closed behaviour stand.

# Context

Adding an LLM touched about seven places in three projects (the agent's if/else, the API's backend list and key map, the CLI's copy of both, scripts and docs). The agent was Python, the only non-Go code in a Go project, and prompts go to third-party vendors, so which vendors are reachable is also a data-governance question (D6 is open).

# Decision

- **One catalog** (`providers/`, a pure Go module, YAML) is the single source of truth and the allowlist of vendors that may receive prompts. The API, the CLI's direct path and, through the Task env they build, the agent all use it. A user picks a name and a model, never a URL, an adapter or a key variable. It validates strictly and fails closed; the service stops on an invalid catalog.
- **A port, not a vendor switch.** The agent depends on `llm.Provider` and a registry of adapters. One adapter wraps Mozilla.ai's `any-llm-go` (pinned v0.9.0, pre-1.0, Apache-2.0) for Anthropic, OpenAI, DeepSeek, Gemini, Groq, Mistral and Ollama; `openai-compat` covers any OpenAI-style endpoint at a configured `base_url`; `echo` is offline. The port is the swap point back to per-vendor SDKs.
- **The agent is Go** ("everything is Go"): a static binary on `debian:bookworm-slim`, no Python. It re-validates the base URL and key variable name with the catalog's rules and redacts the key from `error.txt`.
- **The API validates against the catalog** (`app.ProviderCatalog` port) and builds the Task env from the resolved provider; with `--api` the CLI leaves validation to the server. A provider disabled after acceptance fails the task permanently.
- Shipped catalog: `claude`, `openai`, `echo` enabled; `deepseek` present but disabled until the vendor and data questions are answered.

# Consequences

- A vendor that speaks the OpenAI API is a catalog entry; a native `any-llm-go` vendor is one constant and one map line plus a test-checked adapter list; another protocol is one adapter file.
- The catalog is trusted operator config: its `key_env` names a variable the API copies into the sandbox, so it must be reviewed like a deployment change.
- Credential delivery is unchanged and still the dev shortcut (plaintext in the Task env); how keys reach sandboxes on GKE stays decision D5, and the catalog gives it one place to change.
- Python is gone from the sandbox image; tasks that expect `python3` there need it added deliberately.
