---
type: External Dependency
title: Agent Substrate
description: Sandboxed actor runtime on Kubernetes that AX schedules tasks onto; install, components and the pieces we configure.
resource: https://github.com/agent-substrate/substrate
tags: [substrate, external, gvisor, upstream]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-13T20:00:00Z }
stale_after: 2026-12-31T00:00:00Z
sources:
  - id: gvisor
    resource: https://github.com/agent-substrate/substrate/blob/944abe3278b895ccbf5d45555a49dd0f2f6ceae7/manifests/ate-install/sandboxconfig-gvisor.yaml
    title: gVisor sandbox config with per-arch assets
  - id: apiflag
    resource: https://github.com/agent-substrate/substrate/blob/944abe3278b895ccbf5d45555a49dd0f2f6ceae7/cmd/ateapi/main.go
    title: ate-api-server flags including egress-gateway-address
---

Pinned at `944abe3` (2026-09-18), the commit AX depends on.

# Pieces in the dev cluster (`ate-system`)

`ate-api-server`, `ate-controller`, `atelet` (DaemonSet), `atenet-router`, `atenet-egress`, `postgres`, `rustfs`, plus telemetry in `otel-system`. Installed by `hack/install-ate-kind.sh --deploy-ate-system` (`make substrate-up`), which also works against a k3d context.

# Facts worth knowing

- gVisor `runsc` is downloaded by `atelet` per CPU architecture (amd64 and arm64 both declared),[^gvisor] not provided by the node image.
- Actors run on **WorkerPool** pods (`ateom-gvisor`); capacity comes from the pool's resource limits.
- Snapshots go through an object-store backend: S3/rustfs by default in the kind overlay, GCS otherwise. Dev swaps in [floci](/components/floci.md).
- Egress: `ate-api-server --egress-gateway-address` tunnels actor traffic through `atenet-egress`, which denies everything for an actor without an `EgressPolicy`; an empty value disables tunneling.[^apiflag] See [egress decision](/decisions/004-direct-egress-and-mss-clamp-in-dev.md).
- The pinned `kubectl-ate` has no `create egress-policy`; the newer Substrate redesigned the rule schema (`http`/`https`/`tls_passthrough`), so its client does not work against the pinned server.
- atelet rewrites `localhost:*` image references to `kind-registry:5000`.

[^gvisor]: gVisor sandbox config with per-arch assets
[^apiflag]: ate-api-server flags including egress-gateway-address
