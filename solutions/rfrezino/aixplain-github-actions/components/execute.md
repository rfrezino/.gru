---
tags: [aixplain]
type: Module
title: execute / main entry
description: CLI parsing in main.py and provider selection in execute.py.
resource: src/execute.py
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
# Behavior

`main.py` parses CLI args and calls `execute()`. It splits the ignore and instruction strings on ";", builds a [GithubPR](/components/github-pr.md), and runs [ChatGPT](/components/chatgpt.md) when `openai_token` is non-empty, else [Google Gemini](/components/google-gemini.md). It raises ValueError if both tokens are None.
