---
type: Decision
title: Use k3d instead of kind or vind for local development
description: kind and vind cannot start nodes on the current Docker Desktop VM; k3d can.
tags: [decision, k3d, kind, docker-desktop]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-13T20:00:00Z }
sources:
  - resource: dev/k3d.yaml
---

# Context

Substrate's quickstart uses kind. On an Apple M2 Pro with Docker Desktop (VM kernel 7.0.14-linuxkit, cgroup v2), kind v0.30.0 and v0.33.0 (node images v1.37 and v1.32) and vind (vcluster 0.37.2, docker driver) all failed: systemd units inside the node exit 255/EXCEPTION (kubelet pre-start, journald). A Docker Desktop restart did not change this. OrbStack is not permitted in the owner's environment.

# Decision

Use k3d (k3s has no systemd in the node) as the project default.

# Consequences

- Substrate's kind-oriented installer needs adjustments: Docker volumes for the atelet/ateom hostPaths, a registry named `kind-registry`, and the feature-gate flags. See [dev environment](/components/dev-environment.md).
- Substrate's own scripts still use the "kind" overlay name.
- Revisit if the Docker Desktop VM changes; colima remains the untested alternative.
