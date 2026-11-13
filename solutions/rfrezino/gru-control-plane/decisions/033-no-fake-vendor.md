---
type: Decision
title: No fake vendor in the running system; every call needs a real Claude key
description: "GRU_ANTHROPIC_BASE_URL and the dev fake vendor service are removed from the dev cluster, manifests, Makefile, scripts, cluster tests and docs: the system always calls Anthropic, a missing or rejected key fails with a clear sentence, and fakes live only in-process (--fake, unit tests)."
tags: [decision, llm, providers, dev, testing, gateway]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-06T23:50:00Z }
sources:
  - resource: .claude/thoughts/plans/in-progress/2026-11-06-solutions-overview-followups.md
  - resource: deploy/gru-api/overlays/k3d/kustomization.yaml
  - resource: tests/support/cluster.py
  - resource: docs/operations.md
---

# Context

[Decision 030](/decisions/030-fixed-claude-provider-and-organization-api-key.md) kept a dev seam: `GRU_ANTHROPIC_BASE_URL` pointed the fixed provider and the key check at a fake vendor deployed in the k3d cluster, so the metered path ran with a dummy organization key and no Anthropic account. In practice it let the dev stack and the cluster tests pass on behaviour no real customer would see: Solutions Overview runs on the dev stack ended `empty_bundle` because every answer was "fake answer", and a running system that can silently talk to a fake is a risk the owner does not want to carry.

# Decision

On 2026-11-06 the owner decided that the running system never uses a fake vendor.

1. **Removed completely.** `GRU_ANTHROPIC_BASE_URL`, the `fakevendor` image, deployment and egress rule (`dev/fakevendor/`, `make fakevendor-image`, `make fakevendor-up`), the variable in the k3d overlay and its manifest tests. The key check at save time always goes to Anthropic.
2. **Everything needs Claude configured.** With no key a call fails with a clear message that no vendor is enabled; with a key Anthropic rejects it fails with a clear message that the key is invalid. The sentences the product shows:
   - No key (gateway 403 `gru_no_api_key`, submit 400): `No Claude API key is set for this organization, so no model vendor is enabled. An owner can add one under Settings, Claude API key.`
   - Rejected at call time (gateway 401 `gru_invalid_api_key`): `Anthropic rejected this organization's Claude API key; it is invalid or revoked. An owner can replace it under Settings, Claude API key.`
   - At save, an invalid key is 400 `invalid_llm_key` and an unreachable Anthropic is 503 `llm_key_unchecked`.
   - Solutions Overview repositories fail with `no_api_key` and `invalid_api_key`, with the same meanings ([docs](/components/gru-api.md)).
3. **Fakes stay in-process only.** `--fake` and the unit tests keep fake calls; the fake vendor server moved under `api/tests/support/`, and a `--fake`-only `GRU_FAKE_UPSTREAM_URL` lets the shipped-process acceptance tests point the gateway at it. Neither exists outside `--fake`.

# Consequences

- Dev needs a real Claude key saved by an owner in the product (Settings, Claude API key). There is no way to run the metered path without an Anthropic account except `--fake` and the unit tests. `echo` still works in dev.
- The cluster tests that call Claude (`make api-cluster-test` metered tests, quota, MCP, delegation and `make codesearch-real`) are owner-run, in no aggregate, and spend real money. They check as a precondition that the dev organization has a key (`the dev organization has no Claude API key: an owner saves one under Settings, Claude API key, then run again`), never set one, and assert through the product (task usage, status, ledger) instead of what a fake vendor saw. Tests that scripted the model with `TOOL ...` lines now ask the real model in plain words and depend on it following the instruction.
- Beta: no shim, alias or deprecation path; the variable is simply unknown. Decision 030 carries an amendment note and its history is unchanged.
- Customer impact: a customer with no key or a revoked key sees a plain sentence saying what to do and who can do it, instead of a failure that looks like an outage.
