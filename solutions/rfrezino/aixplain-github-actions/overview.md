---
type: Project
title: AIxplain GitHub Action overview
description: Docker GitHub Action that reviews PR files with ChatGPT or Google Gemini and posts comments.
tags: [github-actions, ai, code-review]
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
# Overview

A Docker-based GitHub Action that reviews the files of a pull request using an LLM and posts per-file comments plus a summary comment.

Flow: [action.yml](/action-inputs.md) -> `entrypoint.sh` -> `src/main.py` -> [execute](/components/execute.md), which picks [ChatGPT](/components/chatgpt.md) if an OpenAI key is given, otherwise [Google Gemini](/components/google-gemini.md). Both extend [AiAssistent](/components/ai-assistent.md) and use [GithubPR](/components/github-pr.md).

Tests live in `tests/unit` and `tests/integration`. The image is built from python:3.11-slim (Dockerfile).
