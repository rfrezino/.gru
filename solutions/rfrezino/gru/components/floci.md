---
type: External Dependency
title: floci-gcp
description: Local GCP emulator that stands in for every GCP service gru uses in dev, namely GCS for snapshots and Cloud SQL (a real MySQL 8.4) for the store.
resource: https://floci.io/gcp/
tags: [floci, gcs, cloud-sql, dev, emulator]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-13T20:00:00Z }
sources:
  - resource: dev/floci/floci-gcp.yaml
  - resource: dev/floci/create-sql-job.yaml
  - resource: dev/scripts/db-up.sh
  - resource: https://github.com/floci-io/floci-gcp
---

Image `floci/floci-gcp` (pinned by digest in `dev/floci/floci-gcp.yaml`, arm64 available), version 0.10.0 at the time of writing, port 4588.

# Wiring

`make floci-up` deploys it, creates the `ate-snapshots` bucket with a Job (idempotent), then sets `STORAGE_EMULATOR_HOST=http://floci-gcp.floci.svc:4588` on `atelet` and `ate-api-server` and removes their S3 settings.

# Cloud SQL and other container-backed services

The project targets GKE and floci mimics it, so dev uses floci for any GCP infra gru needs rather than a one-off substitute (decision: [floci provides all GCP-shaped infra](/decisions/009-floci-provides-all-gcp-shaped-infra.md)). Floci runs its container-backed services (Cloud SQL as `mysql:8.4.11`, Cloud Run) as Docker containers, so the pod has a privileged Docker-in-Docker native sidecar reached through `DOCKER_HOST=tcp://127.0.0.1:2375`; the host Docker socket is never exposed. Those containers live on the sidecar's private bridge, so a `sql-proxy` sidecar forwards port 3306 to wherever floci reports the instance `gru-db` and the Service `gru-db` publishes it.

`make db-up` provisions the instance through the Cloud SQL Admin API (`dev/floci/create-sql-job.yaml`): instance `gru-db` (project `local`, `MYSQL_8_4`), database `gru`, user `gru` with a generated password in the Secret `gru-db-credentials`. Data lives in the pod, so a floci restart means rerunning it.

# Verified

On an explicit suspend/resume, atelet uploads snapshot objects and reads golden checkpoint images through it, and the task keeps its workspace. A plain apply/resume/run never touches object storage on a single node.

# Caveats

- It emulates GCP APIs, not GKE (no Workload Identity, node pools or Artifact Registry).
- `STORAGE_EMULATOR_HOST` is process-wide, so it would also redirect atelet's anonymous gVisor download from `gs://gvisor`; `floci-up` waits until the binaries are cached before switching.
- The pod must set `enableServiceLinks: false`: Kubernetes injects `FLOCI_GCP_PORT=tcp://...` for a service named `floci-gcp`, which floci reads as its port setting and crashes.
- Pre-1.0 project; its feature claims come from its own site. See [decision](/decisions/005-floci-for-gcs-emulation.md).
