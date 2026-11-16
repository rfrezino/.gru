---
type: Decision
title: Emulate GCS with floci in dev
description: Dev snapshots use the GCS code path through floci-gcp instead of Substrate's S3 default.
tags: [decision, floci, gcs, gke]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-13T20:00:00Z }
sources:
  - resource: Makefile
  - resource: dev/floci/floci-gcp.yaml
---

# Context

Substrate's default (non-S3) storage backend is GCS, which is what GKE uses; its kind overlay switches to S3/rustfs, so dev exercised a different path than GKE.

# Decision

`make floci-up` runs [floci-gcp](/components/floci.md) in-cluster and points atelet and ate-api-server at it with `STORAGE_EMULATOR_HOST`.

# Consequences

- Same GCS client code path as GKE, including compose and ranged reads used for snapshots.
- It must be switched on only after atelet has cached the gVisor binaries.
- If floci proves insufficient, rustfs (S3) is the working fallback.
- Not a substitute for testing on real GKE.
