---
type: "Component"
title: "gru-api deployment (k3d)"
description: "How gru-api runs inside the dev cluster: manifests, network policies, tokens, and the Go tests that check them."
---

# Install

`make task-image db-up api-image`, then `make api-up` (add `GRU_API_KEYS=none` to deploy without any provider key). `make api-down` removes the namespace `gru-system`. `dev/scripts/api-up.sh` creates the Secrets from stdin or a private temp directory, so no secret value ever appears in an argument list or in output.

# What is deployed

`deploy/gru-api/base` has no dev shortcut; `overlays/k3d` adds the task image, the egress rule to the floci Cloud SQL proxy and the dev-only optional `gru-api-keys` Secret.

- A Deployment with 2 replicas, a `migrate` init container (migrations take a MySQL named lock), probes on `/healthz` and `/readyz`, non-root, read-only root filesystem, no capabilities, no service account token; JSON logs; a PodDisruptionBudget (`minAvailable: 1`).
- Tokens: the Secret `gru-api-tokens` holds only SHA-256 hashes (`requester:sha256hex`), mounted read-only. `api-up.sh` generates two dev tokens (`dev`, `dev-other`) once and keeps the plaintext in `.cache/api-token` and `.cache/api-token-other` (0600, git-ignored).

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

`make api-cluster-test` runs Go tests (`tests/e2e/cluster`, build tag `cluster`): replicas, non-root and read-only; unlabelled pods denied after a control request proves their network works; AX unreachable even from a labelled pod; egress allowed to AX, the router and MySQL and blocked elsewhere; no token and a wrong token give 401; another requester gets 404; `/metrics` isolation; the PDB; deleting a replica mid-run loses no task; nothing sensitive left in AX or the logs.

The policy engine learns a new pod a few seconds late, so a not-yet-learned pod looks denied; the tests retry what must succeed.

Related: [gru-api](/components/gru-api.md), [dev environment](/components/dev-environment.md), [bearer tokens](/decisions/008-bearer-tokens-and-ownership.md).

# LLM provider catalog

By default gru-api uses the catalog embedded in the binary. To serve an operator's catalog, an overlay includes the component `deploy/gru-api/components/providers` and a ConfigMap `gru-api-providers` holding `providers.yaml`; the component mounts it and sets `GRU_PROVIDERS_FILE` (a catalog that does not validate stops the pods at start). In k3d the dev Secret `gru-api-keys` is injected with `envFrom`, and `dev/scripts/api-up.sh` builds it from the key variables the catalog names (`gru-api --print-key-envs`), so a vendor added by catalog needs no script or manifest change. See [LLM providers](/components/llm-providers.md).
