---
type: "Decision"
title: "Workflows can be kept in a .gru repository and synced both ways, the repository winning"
description: "An organization may switch on GitOps for its workflows: workflows/<name>.yaml in <account>/.gru is compared with the database against a stored base, the repository wins, gru's own changes are committed directly to the default branch as fast-forwards, and polling with a per-organization lease drives it."
---

# Context

Workflows are stored only in gru's database, so a team cannot review, version or roll them back with the tools it uses for code, and a workflow edited in the editor has no history outside gru. The owner asked for GitOps: a GitHub repository named `.gru` holding the organization's workflows, kept in sync with gru, with the repository as the source of truth, direct commits for changes made in gru, polling instead of webhooks, and a complete guide to authoring workflows. Applying configuration means the database rows; nothing is applied to Kubernetes, and a run still starts a workflow by name from the database.

# Decision

- **Three-way compare against a stored base.** `gitops_files` keeps what both sides held at the last sync. For each name: if the repository and the database agree, nothing; else if the repository differs from the base, the repository wins (save the file, or archive the workflow when the file is gone); else if only gru differs, write or delete the file. This is one pure function (`domain/gitops/reconcile.py`) and tells a deletion from a new file, and who changed what.
- **The repository wins.** Only activation exports gru content first, and only for names the repository lacks. Where both have a file, the repository's content is kept.
- **Direct fast-forward commits.** Gru commits `workflows/<name>.yaml` straight to the default branch of `<account>/.gru`: blobs, a tree on the head it read, one commit with that head as its only parent, and a ref update with `force: false`. A moved head or a 422 is `HeadMoved`: nothing is overwritten and the next pass applies the pushed change. The adapter refuses any path outside `workflows/<name>.yaml`; the token has `contents: write` only. The repository must allow direct commits by the App.
- **Polling with a per-organization lease.** Every replica runs the loop; `claim_due` takes due organizations with `FOR UPDATE SKIP LOCKED` and a two-minute lease, and sync-now takes the same lease (409 when held). `GRU_GITOPS_INTERVAL` (5 s to 1 h, default 60 s) is also the backoff step. A poll costs one head lookup when neither side changed.
- **Layout and names.** One file per workflow, `workflows/<name>.yaml`, the file name equal to the YAML `name:`; only direct children count and subdirectories are ignored (reserved for reusable steps). A bad file is a problem on its path, never overwritten, and the last good version stays.
- **A fixed repository in a chosen account.** The repository is always `<account>/.gru`, and the account must be an enabled installation of the organization; activation needs the repository to have a commit.
- **Provenance and identity.** Changes from the repository are saved as `gitops:<8-char commit>` by a service principal built in the use case (no membership row, no member quota); commit messages name the workflow and the saving user, never the text.
- **No role change.** Saving stays open to any member as today; activation, deactivation and sync-now need admin, owner or a platform operator. A `read` role is a separate plan.
- **Approved surface.** Routes `GET /v1/gitops`, `PUT /v1/gitops` and `POST /v1/gitops:sync`, the variable `GRU_GITOPS_INTERVAL`, the series `gru_gitops_syncs_total` and `gru_gitops_changes_total`, and migration `0006_gitops` (`gitops_config`, `gitops_files`). No MCP tools or UI page in this slice.

# Consequences

- The repository must allow direct commits by the App; otherwise changes made in gru are not written (the status says so) while repository-to-gru sync continues.
- A change made in gru can be replaced by a push that arrived first. The earlier version stays in the name's history.
- A file with a problem makes the pass re-read the directory each poll until it is fixed, trading a few GitHub calls for picking up a fixed quota or allow-list without a new push.
- Reusable steps and subdirectories of `workflows/`, hooks and other configuration in the repository, webhooks, MCP tools and a UI page are later work.
- Deactivating leaves both sides as they are; activating again starts from an empty base.
