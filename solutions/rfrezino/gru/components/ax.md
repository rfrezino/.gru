---
type: External Dependency
title: google/ax
description: Declarative agent-task orchestrator on Kubernetes; what it provides and what it does not.
resource: https://github.com/google/ax
tags: [ax, external, upstream]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
stale_after: 2026-12-31T00:00:00Z
sources:
  - id: server
    resource: https://github.com/google/ax/blob/ac2332829f22360ff97b0ba34d94dd0dd782f17e/internal/server/server.go
    title: ax-server gRPC server setup
  - id: proto
    resource: https://github.com/google/ax/blob/ac2332829f22360ff97b0ba34d94dd0dd782f17e/pkg/apis/v1alpha1/ax.proto
    title: ax.v1alpha1 API
  - id: queue
    resource: https://github.com/google/ax/blob/ac2332829f22360ff97b0ba34d94dd0dd782f17e/internal/lock/lock.go
    title: resource locking that replaced the queue
---

Pinned at `ac23328` (2026-09-27); pre-1.0 and breaking changes are expected.

# What it is

A control plane exposing the gRPC service `ax.v1alpha1.AX` (`Create/Get/List/Delete/Suspend/Resume/WatchTask`, plus Workspace and Model CRUD).[^proto] The backend uses a Go client generated from the vendored proto (`make api-proto`) rather than importing AX. Tasks are immutable once created. Each task gets its own Substrate ActorTemplate named `<task>-tmpl-<hash>`.

# Behaviours that matter here

- **No authentication or TLS** on its own API: the server is `grpc.NewServer()` with no options.[^server] It must only be reachable from trusted callers.
- **No queue**: direct execution with resource locking.[^queue] No free worker surfaces as `ResourceExhausted: no free workers`.
- **New tasks start `Suspended`**; resume them. Resuming right after creation can fail with "golden tag not available" (see [troubleshooting](/runbooks/troubleshooting.md)).
- **No task output or exit code** is returned to the caller.
- Only a Gemini key is injected automatically; task `env` entries are plain `name`/`value` (no secret references).
- `AX_SNAPSHOTS_BUCKET` falls back to a Google-internal bucket when empty, so every overlay must set it.
- A Substrate **WorkerPool** must exist or tasks fail to schedule; it lives in `deploy/workers`.
- Must run against the Substrate commit in its `go.mod`: see [pinning decision](/decisions/002-pin-substrate-to-ax-dependency.md).
- **Quirks the backend hides** (observed on the pinned stack, recorded in `.claude/thoughts/investigations/2026-10-14-ax-adapter-findings.md` and pinned by `errors_test.go` in the AX adapter): creating an existing task fails with `FailedPrecondition ... already exists`; a resume with no free worker arrives as gRPC `Internal` with `ResourceExhausted` only in the message and leaves the stored phase `Failed`; AX phase never means "done"; `WatchTask` adds nothing over polling; delete of a missing task is `NotFound`; `ListTasks` has no prefix filter.
- In the dev cluster `ax-server` accepts connections only from [gru-api](/components/gru-api.md) (a NetworkPolicy); operator port-forwards are unaffected.

[^server]: ax-server gRPC server setup
[^proto]: ax.v1alpha1 API
[^queue]: resource locking that replaced the queue
