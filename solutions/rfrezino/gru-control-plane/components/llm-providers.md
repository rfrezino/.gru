---
type: Component
title: LLM providers (fixed list and organization key)
description: Which LLM provider receives prompts (one, fixed in code), how an organization's own Claude API key is stored and used, and how the sandbox agent talks to the model.
resource: providers/
tags: [llm, providers, claude, kms, gateway, agent]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-06T22:00:00Z }
sources:
  - resource: providers/gru_providers/fixed.py
  - resource: api/gru_api/adapters/providers/fixed.py
  - resource: api/gru_api/domain/use_cases/llm_key.py
  - resource: api/gru_api/adapters/secretbox/sealer.py
  - resource: api/gru_api/adapters/kms/wrapper.py
  - resource: api/gru_api/host/gateway/proxy.py
  - resource: images/task/agent/gru_agent/adapters/registry.py
---

> Replaces the catalog this page used to describe: [decision 030](/decisions/030-fixed-claude-provider-and-organization-api-key.md) made the list a constant and gave every organization its own key; [decision 010](/decisions/010-pluggable-llm-providers-through-a-catalog.md) is superseded in part.

# The fixed list

`providers/gru_providers/fixed.py` is the single source: provider `claude-code` (adapter `anthropic`, metered through the gateway, runner `claude-code`) in every environment, plus `echo` only when `GRU_ENV=dev`. There is no catalog file and `GRU_PROVIDERS_FILE` is retired (nothing reads it). The four models, the default (`claude-sonnet-5-5`) and the prices in micro-USD per million tokens are constants. `claude` is an alias of `claude-code`; `backend` on a task or run is optional and any other name is a 400. `GET /v1/providers` and the MCP tool `list_providers` return this list; `adapters/providers/fixed.py` serves it to the API as `IProviderCatalog`.

Adding a provider is a code change with its own decision record: an entry in `fixed.py`, an adapter in the agent's registry (`images/task/agent/gru_agent/adapters/registry.py`; a test keeps the registry and `fixed.py` equal), a gateway wire if it is metered, and a decision.

# Which runner

By rule, not by configuration: chat tasks, review stages and `echo` run on `gru-agent` (`runner: gru-agent`, the `anthropic` SDK adapter); every other stage runs the pinned Claude Code CLI (`runner: claude-code`), which is also where skills and plugins load. Both reach the model through the gateway.

# The organization's Claude key

Each organization saves its own Claude API key (`/v1/organization/llm-key`, the Claude API key page at `/settings/claude-key`). An owner or a Gru admin writes; any member reads the status (set or not, last four characters, when and by whom). The key is checked with Anthropic when saved, sealed with AES-256-GCM under a data key made for that organization, and the data key is stored only wrapped by Cloud KMS (`GRU_SECRETS_KMS_KEY`); no key that opens every organization's key exists anywhere. The gateway reads the key per call through `ILlmKeyResolver` and forwards with it; the sandbox only ever has its per-task gateway token. An organization with no key is refused at submit (400) and at the gateway (403 `gru_no_api_key`). Details and the failure mapping (a wrong KMS key name is loud, a destroyed key version is "no key"): [decision 030](/decisions/030-fixed-claude-provider-and-organization-api-key.md), `docs/organizations.md` and `docs/operations.md`.

# The agent port

`images/task/agent` (`gru_agent`) has a `Provider` port with two adapters: `anthropic` (the SDK, pointed at the gateway through `GRU_BASE_URL`) and the dev-only offline `echo`. The agent re-validates the base URL and the key variable name with `gru_providers.catalog` (`validate_base_url`, `validate_key_env`, the two checks that remain of the old catalog), so a tampered Task env cannot redirect prompts. Error text has the key redacted before it reaches `error.txt`.

# Dev

`echo` needs no key and `make ask BACKEND=echo PROMPT=test` runs without one. There is no fake vendor in the running system ([decision 033](/decisions/033-no-fake-vendor.md)): the metered `claude-code` path needs a real Claude key saved by an owner, and without an Anthropic account the only fakes are `--fake` and the unit tests. With no key the call fails with `No Claude API key is set for this organization, so no model vendor is enabled. An owner can add one under Settings, Claude API key.` (gateway 403 `gru_no_api_key`, submit 400); a key Anthropic rejects fails with `Anthropic rejected this organization's Claude API key; it is invalid or revoked. An owner can replace it under Settings, Claude API key.` (gateway 401 `gru_invalid_api_key`). `GRU_SECRETS_KMS_ENDPOINT` (dev only) points the real KMS client at the floci-gcp emulator, where `make api-up` creates the dev key; floci loses its state when its pod restarts, so the dev organizations enter their key again. It is refused in staging and prod. Components: [task image](/components/task-image.md).
