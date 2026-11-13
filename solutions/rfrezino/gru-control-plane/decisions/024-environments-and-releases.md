---
type: Decision
title: Three environments, build once and promote by digest, expand-only migrations
description: Dev on k3d, staging and production on GKE; CI builds each image once per commit and promotes the same digests; production guards refuse a dev configuration; secrets are mounted files; migrations only expand while a release rolls.
tags: [decision, environments, releases, security]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-02T00:00:00Z }
sources:
  - resource: docs/releases.md
  - resource: docs/operations.md
  - resource: deploy/gru-api/overlays/staging/kustomization.yaml
  - resource: api/gru_api/platform/config_environments.py
---

# Decision

- **Three environments.** Dev stays on k3d. Staging and production are separate GKE
  clusters with the same overlays and different numbers. The GKE spike (does Substrate's
  gVisor worker run on GKE nodes, what MTU and egress appear) is the owner's, on real GCP;
  nothing here assumes its answer beyond what the overlays place as placeholders.
- **Build once, promote by digest.** `images.yml` builds `gru-api`, `gru-codesearch`,
  `gru-task` and the two ko images once per commit and publishes `digests.json`.
  `deploy.yml` renders an overlay and pins every image to its digest
  (`dev/tools/set_digests.py`); production promotes the digests staging ran, behind a GitHub
  Environment with required reviewers. A mutable tag never reaches a cluster.
- **A dev configuration does not start in staging or production.** `GRU_ENV` refuses token
  or no authentication, the PEM and fake GitHub signers, a fake mint, plain http outside the
  cluster, a database without TLS, no operator, secrets from the environment and a missing
  runner token key.
- **Secrets are mounted files.** Secret Manager through the GKE add-on, read-only into
  `gru-api` and `gru-worker` only, never into a sandbox. This settles key delivery (D5) for
  GKE.
- **The runner has a per-task secret.** `HMAC-SHA256(GRU_RUNNER_TOKEN_KEY, sandbox name)`
  is the header `X-Gru-Runner-Token` on every call to the runner; the runner answers 401
  without it and accepts everything when none is configured (old sandboxes, dev).
- **Migrations only expand while a release rolls.** `DROP`, `RENAME`, `alter_column` and a
  `NOT NULL` column with no default belong to a `_contract` revision in a later release.
  Rollback is the previous digest, never a down migration.
- **Not decided here:** decision 013 (task image hardening) is still a proposed spike; it
  needs the cluster. The compliance gate is a signed checklist, not code.

# Consequences

Adds env vars `GRU_KEYS_SOURCE`, `GRU_KEYS_DIR`, `GRU_MYSQL_DSN_FILE`, `GRU_RUNNER_TOKEN_KEY`,
the header `X-Gru-Runner-Token`, the metric `gru_oldest_pending_seconds`, and the `staging`
test marker. Everything existing keeps its name.
