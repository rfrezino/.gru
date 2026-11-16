---
type: Decision
title: Floci provides all GCP-shaped infra in dev
description: Dev uses floci for every GCP service gru depends on (GCS, Cloud SQL, later others), including those that need Docker, instead of one-off substitutes.
tags: [decision, floci, gke, dev]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T22:00:00Z }
sources:
  - resource: dev/floci/floci-gcp.yaml
  - resource: dev/floci/create-sql-job.yaml
  - resource: dev/scripts/db-up.sh
---

# Context

gru is built for GKE and floci mimics GCP. Dev used floci for GCS but a plain MySQL Deployment for the store, so the store path in dev differed from GKE (Cloud SQL). Floci can emulate Cloud SQL, but it does so by starting a real MySQL container through the Docker socket, which the pod in k3d did not have.

# Decision

- Anything gru needs from GCP in dev comes from floci, provisioned through the same admin APIs as on GKE. The one-off dev MySQL (`deploy/dev/mysql.yaml`, `make mysql-up`) is removed; `make db-up` provisions a Cloud SQL instance in floci.
- Floci's pod gets a privileged Docker-in-Docker native sidecar so every container-backed floci service works, not only Cloud SQL. The host Docker socket is not mounted and the k3d cluster does not need rebuilding.
- Because those containers sit on the sidecar's private bridge, a small proxy sidecar publishes the Cloud SQL instance as the Service `gru-db`. This is a dev-only shim; on GKE gru-api uses the Cloud SQL private address.

# Consequences

- Data lives in floci's pod: restart it and rerun `make db-up`, as with the old `emptyDir` MySQL.
- A privileged container in the dev cluster: acceptable for dev only, never in a GKE manifest.
- A new GCP dependency is first tried against floci; if floci lacks it, that is reported as a gap, not worked around with a separate dev service.
