---
type: "Decision"
title: "Dev uses direct sandbox egress and a TCP MSS clamp"
description: "Two dev-only network fixes that let sandboxes reach LLM APIs; neither is valid for a shared cluster."
---

# Context

A sandbox could not call any LLM API: HTTPS to every host timed out. Two independent causes:

1. `ate-api-server` is started with `--egress-gateway-address=atenet-egress...`, so all actor traffic is tunneled through a gateway that denies everything for an actor without an `EgressPolicy`. AX creates actors without one (task egress is on AX's roadmap), and the pinned Substrate CLI cannot create one.
2. With direct egress, small HTTP worked but TLS handshakes and 200 KB downloads hung: the pod network MTU is 1450 (flannel VXLAN) while the gVisor sandbox sends 1500-byte packets.

# Decision

- `make egress-direct` (`dev/scripts/egress-direct.sh`) removes the egress-gateway flag and restarts the workers (they keep old redirect rules).
- `make net-fix` clamps the TCP MSS to 1400 on the k3d node; it is applied by `cluster-up` and must be rerun after a node restart.

# Verified

From a fresh rebuild, a sandbox gets 200 for a 300 KB download and 401 (reachable, unauthenticated) from `api.openai.com` and `api.anthropic.com`.

# Consequences

- Direct egress skips the gateway's identity and policy enforcement: do not copy it to a shared cluster. How tasks get egress on GKE is an open security decision.
- Whether GKE nodes have the same MTU mismatch is unknown.
