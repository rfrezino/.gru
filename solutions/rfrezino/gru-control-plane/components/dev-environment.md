---
type: Component
title: Local development environment
description: k3d cluster, Makefile targets and Tilt resources that reproduce the stack locally.
resource: dev/
tags: [dev, k3d, tilt, make]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
sources:
  - resource: Makefile
  - resource: dev/k3d.yaml
  - resource: Tiltfile
---

# `make up` order

`cluster-up` (k3d + `net-fix`) -> `substrate-up` -> `egress-direct` -> `floci-up` -> `ax-up` -> `workers-up` -> `task-image` -> `ax-bin` -> `gru-bin`. `make down` removes the cluster, registry and the two Docker volumes.

# Backend targets

| Target | What it does |
|---|---|
| `make db-up` | the dev database: a Cloud SQL instance `gru-db` in [floci](/components/floci.md) (real MySQL 8.4, database and user `gru`, Service `gru-db.floci.svc`); data lives in floci's pod, so rerun it if that restarts; the password is generated locally into the Secret `gru-db-credentials` and never printed |
| `make api-dev` | runs [gru-api](/components/gru-api.md) on your machine against the cluster (port-forwards AX, the router and MySQL) |
| `make api-image`, `make api-up`, `make api-down` | build and push the image, deploy into `gru-system`, remove ([deployment](/components/gru-api-deployment.md)); `api-up` also creates the dev KMS key in floci |
| `make unit`, `integration`, `acceptance`, `e2e`, `check` (and `<project>-<tier>`, `api-vuln`) | the test tiers across projects or one by one ([testing](/components/testing.md)), and the vulnerability scan |

# k3d cluster (`dev/k3d.yaml`)

- `rancher/k3s` v1.36.2 with Substrate's feature gates (`ClusterTrustBundle`, `PodCertificateRequest`, `certificates.k8s.io/v1beta1`) and parallel image pulls; traefik disabled.
- Docker volumes `gru-ate-data` and `gru-ateom-gvisor-data` mounted at `/var/lib/ate` and `/var/lib/ateom-gvisor` (overlayfs rejects an overlay2 upperdir); they must exist from node start because atelet and the workers share sockets there.
- Registry container named `kind-registry` on `127.0.0.1:5001` with a `localhost:5001` mirror.

# Tilt

The `Tiltfile` guards the context (`k3d-gru`), rebuilds the task image and CLI on change, and offers manual `ask-echo` / `ask-claude` triggers plus `api-deploy`, `api-cluster-test` and `api-e2e`. Substrate stays under `make` (slow, rarely changes). `tilt ci --context k3d-gru` validates it.

# Upstream checkouts

`.cache/` (git-ignored) holds pinned clones of AX and Substrate; the Makefile exports `ASDF_GOLANG_VERSION` because asdf resolves Go per directory.

Related: [local development runbook](/runbooks/local-development.md), [why k3d](/decisions/001-k3d-instead-of-kind.md).
