---
type: Decision
title: Run artifacts are pulled from the sandbox at settle, stored in a bucket with metadata in MySQL, and the feature is off without a bucket
description: "Files an agent writes under work/artifacts/ are pulled through the runner when a task succeeds or fails, validated, stored in a blob store (GCS, floci in dev, memory in tests) with rows in MySQL, and listed and downloaded by the owner over HTTP, MCP and the web UI; a platform guideline teaches the agent where to write."
tags: [decision, artifacts, storage, gcs, sandbox]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-04T20:00:00Z }
sources:
  - resource: api/gru_api/domain/use_cases/artifacts.py
  - resource: api/gru_api/domain/use_cases/artifact_pull.py
  - resource: api/gru_api/domain/artifact_guidelines.py
  - resource: api/gru_api/adapters/gcs
  - resource: api/gru_api/host/http/routers/artifacts.py
  - resource: ui/src/components/Artifacts.tsx
  - resource: docs/artifacts.md
  - resource: .claude/thoughts/investigations/2026-11-04-run-artifacts.md
---

# Context

An agent's deliverable was either its one answer text or the run's work directory as an archive that expires after 24 hours. People want named files (plans, reports, CSVs, patches) that outlive the job, belong to a run or task and download one by one, and large files must not ride in MySQL.

# Decision

- **Metadata in MySQL, bytes in a blob store.** `IArtifactRepository` (migration `0012_artifacts`) and `IArtifactBlobs` (a GCS adapter, floci-gcp in dev, memory in `--fake` and unit tests, one contract suite per port). The object key is `<organization_id>/<id>`: a name is user data and never part of a key. MySQL blobs were rejected (size cap, backups). New dependency `google-cloud-storage`, proved in the dependency gate.
- **Pull, never push.** Keeping decisions 003 and 006 (the sandbox is closed; the control plane reads), the reconciler asks the runner for `work/artifacts` (`GET /gru/v1/artifacts`) when a task reaches `Succeeded` or `Failed`, before the sandbox is deleted. A gateway upload was rejected: a new auth surface and a client a model with no shell cannot use anyway.
- **The pull never fails a task.** Every error (runner, quota, bad names, store down) leaves the status and the answer unchanged and is counted by `gru_artifacts_total{result}` (`stored|empty|rejected|failed`). One bad name, too many files or a file over the cap rejects the whole pull. The set is replaced per task, so a restart is idempotent.
- **One directory convention, no new tool.** The agent uses `write_file` under `artifacts/`. A platform guideline, a code constant (not a per-organization text), is added to the system message after hook composition, for plain tasks and stages, never for chat or a hook's agent task. A plain task gets file tools that write only under `artifacts/` (`GRU_WRITE_PREFIX`); stages keep full access.
- **The feature is off without a bucket.** `GRU_ARTIFACT_BUCKET` empty means no pull, no guideline and empty lists, so a deployment without GCS behaves as before; staging and prod set it in their overlays and the platform team owns the bucket and Workload Identity.
- **Reading goes through gru-api.** `GET /v1/tasks/{id}/artifacts`, `GET /v1/workflow-runs/{id}/artifacts`, `GET /v1/artifacts/{id}` (bytes, `Content-Disposition: attachment`, `X-Gru-Artifact-Sha256`) and the MCP tools `list_artifacts` and `read_artifact` (55 names). Visibility follows the task and run use cases; others get 404. No signed URLs.
- **Lifecycle and quota.** Expiry is `GRU_RUN_RETENTION` (organization override); the sweeper deletes the blob and then the row; an organization purge has an `ARTIFACTS` step; artifact bytes count in the storage quota and a pull over the cap is rejected.
- **UI.** An Artifacts card on the task and run pages: download through the bearer token, a preview of text, markdown and JSON under 256 KiB, polling every 2 s while the work is live.
- **Approved surface (additive).** Three routes, two MCP tools, series `gru_artifacts_total{result}` (50 series), five `GRU_ARTIFACT_*` variables, sandbox variables `GRU_WRITE_PREFIX` and `GRU_ARTIFACT_MAX_FILE_BYTES`, one table and one runner endpoint.

# Consequences

- Files are kept only if the agent writes them under `artifacts/`; anything else in the work directory is still reachable only through the 24 h workspace archive.
- The agent's own `write_file` caps a file at 1 MiB; bigger files need the Claude Code runner and a larger `GRU_ARTIFACT_MAX_FILE_BYTES` (at most the runner's 16 MiB archive cap).
- A failed pull is silent to the person (a metric, no event); there is no retry beyond the next observation of the task.
- Not built: signed URLs, artifacts shared between stages, per-organization guideline text, uploads from the sandbox.
