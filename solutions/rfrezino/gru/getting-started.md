---
type: Reference
title: Start here
description: How this bundle is organized and which concept to read first.
tags: [getting-started]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
---

# Reading order

1. [Architecture overview](/architecture/overview.md): the end-to-end path of a prompt and what each part does.
2. [Local development](/runbooks/local-development.md): bring the environment up and run a prompt.
3. [Troubleshooting](/runbooks/troubleshooting.md): symptoms seen so far and their causes.
4. [gru-api](/components/gru-api.md) and its [deployment](/components/gru-api-deployment.md): the backend that runs tasks on AX, stores them and answers over REST.
5. [Test tiers and layout](/components/testing.md): how tests are organized in every project and how to run one tier.
6. [Decisions](/decisions/index.md): why the environment looks the way it does (k3d, pinned Substrate, direct egress, floci, the result channel, both CLI paths, token auth).
7. [What is left](/plans/backend-service.md): the GKE work and the decisions it waits on.

Everything here describes the repository at the time of writing. The repository itself (`README.md`, `CLAUDE.md`, `Makefile`) is authoritative when they disagree.
