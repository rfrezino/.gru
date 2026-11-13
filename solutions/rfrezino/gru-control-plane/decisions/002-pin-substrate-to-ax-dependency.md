---
type: Decision
title: Pin Substrate to the commit google/ax depends on
description: AX builds against Substrate 944abe3; newer Substrate breaks AX's template creation.
tags: [decision, pinning, ax, substrate]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-13T20:00:00Z }
sources:
  - id: axmod
    resource: https://github.com/google/ax/blob/ac2332829f22360ff97b0ba34d94dd0dd782f17e/go.mod
    title: AX go.mod
  - resource: Makefile
---

# Context

With Substrate at a newer commit (`f0df43b`, about 87 commits ahead), AX failed to create task templates (`snapshot_config: unknown field with protobuf tag 1`) and the fallback `default-template` did not exist.

# Decision

`Makefile` pins AX at `ac23328` and Substrate at `944abe3`, the version in AX's `go.mod`.[^axmod] Both are cloned into `.cache/` by `make deps`.

# Consequences

- The installer at that commit lacks newer flags (for example `--credential-provider`), and its `kubectl-ate` lacks `create egress-policy`.
- Bump both pins together, only after checking AX's `go.mod` at the new AX commit, then rerun `make reset`.
- `kubectl-ate` must be built from the pinned Substrate clone.

[^axmod]: AX go.mod
