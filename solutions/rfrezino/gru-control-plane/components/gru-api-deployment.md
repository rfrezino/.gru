---
type: Component
title: gru-api deployment (k3d)
description: "How gru-api runs inside the dev cluster: manifests, network policies, tokens, and the Go tests that check them."
resource: deploy/gru-api/
tags: [deploy, kubernetes, networkpolicy, security, k3d]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
sources:
  - resource: deploy/gru-api/base/deployment.yaml
  - resource: deploy/gru-api/base/networkpolicy.yaml
  - resource: deploy/gru-api/base/worker-deployment.yaml
  - resource: deploy/temporal/deployment.yaml
  - resource: dev/scripts/api-up.sh
  - resource: tests/e2e/cluster/cluster_test.go
---

> Superseded in part by [decision 021](/decisions/021-python-control-plane.md): the cluster tests are pytest (`tests/e2e/cluster`, marker `cluster`), not Go tests; the manifests are unchanged.

# Install

`make task-image db-up api-image`, then `make api-up` (no vendor key is needed: each organization enters its own Claude key, and `api-up` creates the dev KMS key in floci). `make api-down` removes the namespace `gru-system`. `dev/scripts/api-up.sh` creates the Secrets from stdin or a private temp directory, so no secret value ever appears in an argument list or in output.

# What is deployed

`deploy/gru-api/base` has no dev shortcut; `overlays/k3d` adds the task image, the egress rule to the floci Cloud SQL proxy and the dev-only floci KMS endpoint (no fake vendor, [decision 033](/decisions/033-no-fake-vendor.md)).

- A Deployment with 2 replicas, a `migrate` init container (migrations take a MySQL named lock), probes on `/healthz` and `/readyz`, non-root, read-only root filesystem, no capabilities, no service account token; JSON logs; a PodDisruptionBudget (`minAvailable: 1`).
- Tokens: the Secret `gru-api-tokens` holds only SHA-256 hashes (`[org-slug/]user:sha256hex`), mounted read-only. `api-up.sh` generates two dev tokens (`platform/dev`, a platform operator via `GRU_PLATFORM_OPERATORS=dev` in the k3d overlay, and `other/other-user`; it creates the organization `other` through the operator route after the rollout) once and keeps the plaintext in `.cache/api-token` and `.cache/api-token-other` (0600, git-ignored).

# The run worker and Temporal

Workflow runs execute on Temporal ([decision 014](/decisions/014-temporal-runs-the-workflows.md)). `make api-up` first applies `deploy/temporal` (the pinned open-source dev server in the namespace `temporal`, SQLite on an emptyDir, so dev state is lost with the pod) and then the `gru-worker` Deployment next to `gru-api`: the same image started with `--worker --no-http`, two replicas, `/metrics` as its liveness check, the same hardening and secrets. Both processes run the task reconciler (row leases make that safe). `make temporal-up` and `make temporal-down` manage the server alone.

Network rules: the Temporal frontend accepts only `gru-api` and `gru-worker` pods; both may reach it on 7233 and nothing else is added to their egress; `ax-server` and `atenet-router` accept the worker as they accept `gru-api`; the k3d database rule covers both. `TestTemporalIsReachableOnlyFromGruPods` checks the frontend from an arbitrary pod, with and without the client label.

# Network policies

| Rule | Effect |
|---|---|
| ingress to `gru-api` | only pods labelled `gru.io/api-client=true`, any namespace, port 8080 |
| ingress on 9090 | only pods labelled `gru.io/metrics-scraper=true` (the Service does not publish it) |
| egress from `gru-api` | DNS, AX, the router and (k3d overlay) MySQL; nothing else |
| ingress to `ax-server` | only `gru-api`; port-forwards by operators are unaffected |

Not yet covered: `atenet-router` ingress, which is still open to every pod ([remaining plan](/plans/backend-service.md)).

# Checks

`make api-cluster-test` runs Go tests (`tests/e2e/cluster`, build tag `cluster`): replicas, non-root and read-only; unlabelled pods denied after a control request proves their network works; AX unreachable even from a labelled pod; egress allowed to AX, the router and MySQL and blocked elsewhere; no token and a wrong token give 401; another organization's task, workflow and run get 404; `/metrics` isolation; the PDB; deleting a replica mid-run loses no task; nothing sensitive left in AX or the logs.

The policy engine learns a new pod a few seconds late, so a not-yet-learned pod looks denied; the tests retry what must succeed.

Related: [gru-api](/components/gru-api.md), [dev environment](/components/dev-environment.md), [bearer tokens](/decisions/008-bearer-tokens-and-ownership.md).

# LLM providers

The provider list is fixed in code and no provider key is mounted anywhere: each organization's Claude key lives sealed in MySQL, wrapped by the Cloud KMS key that `GRU_SECRETS_KMS_KEY` names (in k3d a floci key; in staging and prod a ConfigMap value). `GRU_PROVIDERS_FILE` is retired and the `providers` kustomize component is gone. See [LLM providers](/components/llm-providers.md).
