---
type: "Component"
title: "LLM providers (catalog and port)"
description: "How gru decides which LLM vendors may receive prompts and how the sandbox agent talks to them, so adding a vendor is configuration."
---

> Superseded in part by [decision 021](/decisions/021-python-control-plane.md): the catalog (`providers/gru_providers`) and the agent's `Provider` port are Python; the adapters use the `anthropic` and `openai` SDKs, not `any-llm-go`.

# Two pieces

- **The catalog** (`providers/`, a pure Go module): a YAML list of the providers gru may send prompts to. It is the operator-controlled allowlist: a user picks a name and a model, never a URL, an adapter or a key variable. It validates strictly and fails closed (unknown fields, `key_env` must look like `VENDOR_API_KEY`, `base_url` must be https except for localhost and in-cluster hosts). The default (embedded, replaceable with `GRU_PROVIDERS_FILE`) enables `claude`, `openai` and `echo`; `deepseek` is a disabled example until the vendor and data questions are answered. An entry can list `models`: the models people may pick, which the UI shows as a list (`GET /v1/providers`). When the list is not empty it is also an allowlist (the API and the direct CLI path refuse any other model, naming the available ones). Model names change, so the shipped lists (Claude Opus, Sonnet and Haiku; OpenAI GPT-6 Astra, 6.1 Sol and Luna, taken from the vendors' docs, not checked against a key) are data to edit in the YAML, not code.
- **The agent port** (`images/task/agent/internal/llm`): `Provider.Complete(ctx, Request) (string, error)` and a `Registry` of adapters by name. One adapter wraps Mozilla.ai's `any-llm-go` (pinned, pre-1.0) for Anthropic, OpenAI, DeepSeek, Gemini, Groq, Mistral and Ollama; `openai-compat` serves any endpoint speaking the OpenAI chat API at the entry's `base_url`; `echo` is offline. The port is the swap point if the library is ever replaced by per-vendor SDKs.

# Adding a vendor

- It speaks the OpenAI chat API (Grok, hosted Llama or Qwen, a local server): add a catalog entry with `adapter: openai-compat`, `base_url` and `key_env`. No code.
- `any-llm-go` supports it natively: add the adapter name to `providers.Adapters()` and one line to the `native` map in `anyllm.go`; a test fails if the two lists disagree.
- Another protocol: one adapter file and one `Register` call.

# In the API

`app.ProviderCatalog` is the port (`Lookup`, `Names`); `adapters/out/providers` wraps the catalog module. `Submit` validates the backend and the model against it (an unknown or disabled name is a 400 listing the enabled ones; the model is required only where the catalog says so). The AX adapter resolves the provider again at start, so a provider disabled after a task was accepted fails that task permanently instead of retrying.

# Keys in dev

The catalog names the variable each provider reads (`key_env`); it never holds a credential. `gru-api --print-key-envs` prints those names for the enabled providers, and `dev/scripts/api-up.sh` and `api-dev.sh` read exactly those variables from the shell or the gitignored `.env` (the shell wins; a blank entry never overrides it). In the cluster, an operator catalog is mounted with the `deploy/gru-api/components/providers` kustomize component.

# Boundaries

Keys stay out of the catalog; how they reach the sandbox is decision D5. Error text has the key redacted before it reaches `error.txt`. Why it is built this way: [decision 010](/decisions/010-pluggable-llm-providers-through-a-catalog.md). Components: [task image](/components/task-image.md).
