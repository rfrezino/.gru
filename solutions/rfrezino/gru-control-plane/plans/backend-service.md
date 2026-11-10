---
type: "Plan"
title: "Backend service: what is left (GKE hardening)"
description: "The backend is built and verified on k3d; this is the remaining work to run it on a shared GKE cluster and the decisions it waits on."
---

The planned backend now exists: see [gru-api](/components/gru-api.md) and its [deployment](/components/gru-api-deployment.md). What it does not have is everything specific to a shared GKE cluster. The detailed plan with work items and exit criteria is `.claude/thoughts/plans/2026-10-14-backend-phase-6c-gke.md`.

# Decisions it waits on

| Decision | Options | Blocks |
|---|---|---|
| Identity provider (D2) | The organisation's OIDC (issuer, audience, which claim is the requester) vs static tokens for services only | OIDC authenticator, any exposure beyond the cluster |
| Provider-key delivery | gateway in the backend, credential injection through Substrate, or AX support for secret references | removing the plaintext-key shortcut |
| GKE egress (D4) | Substrate egress gateway with an `EgressPolicy` vs direct; whether the MTU problem exists there | key delivery, overlays |
| Data classification and retention (D6) | may prompts and answers be stored, and for how long | real customer data |
| GCP project, registry, database | Cloud SQL for MySQL 8.4, Workload Identity | overlays, database |

# Work that can start without a decision

- An OIDC `Authenticator` built against a test issuer (the seam exists, see [decision 008](/decisions/008-bearer-tokens-and-ownership.md)).
- A NetworkPolicy so only `gru-api` can reach `atenet-router` (today any pod can read any task's result through it, see [decision 006](/decisions/006-result-via-runner-http-and-router.md)).
- Parameterising the cluster tests (context, namespaces, token source) so they can run against GKE.

# Work that needs a cluster or a cloud resource

GKE overlays for AX and `gru-api` (no dev shortcut allowed: plaintext provider keys, `debug: true`, direct egress, dev tokens), Cloud SQL (TLS, managed secret, backups), scrape configuration and alerts, and smaller hardening items (a cap on how long a task may stay `Pending`, per-requester quotas, token rotation without a restart, autoscaling, an owner for upstream pin bumps).

Related: [AX](/components/ax.md), [architecture overview](/architecture/overview.md).
