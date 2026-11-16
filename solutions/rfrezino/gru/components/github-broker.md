---
type: Component
title: GitHub repository broker
description: How runs read and publish GitHub repositories without the sandbox holding credentials.
tags: [github, security]
generated: { by: claude-code/sonnet-5.5, at: 2026-11-16T00:00:00Z }
sources:
  - id: doc
    resource: docs/github-repositories.md
---

# GitHub repository broker

gru connects as a private **GitHub App**, never a personal token. The sandbox holds no GitHub credential and has no network path to GitHub; a trusted broker inside [gru-api](/components/gru-api.md) does the talking.

- The broker has Cloud KMS sign a short-lived JWT (the App key is sign-only in KMS, `GRU_GITHUB_KMS_KEY`), exchanges it for an installation token limited to one repository and `contents:read`, downloads files, revokes the token, and fails closed if GitHub granted a different scope.
- Files are imported into the first stage's `/workspace/work` ([task image](/components/task-image.md)).
- Workflows pin a repository (`repo:`) or let the agent choose (`repos:`, with search by file name); approval lists what changed; publishing opens one **draft pull request** per changed repository and needs write permissions on the App.
- Two fences: the App installation's chosen repositories and gru's allow-list. A GitHub page in the [web UI](/components/ui.md) checks and edits the connection. KMS on GKE is not wired yet.
- Code search over the same repositories: [code-search service](/components/codesearch.md).
