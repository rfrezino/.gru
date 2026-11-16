---
tags: [aixplain]
type: Module
title: AiAssistent base class
description: Abstract review workflow shared by the providers.
resource: src/ai_assistent.py
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
# Workflow (`execute`)

1. Skip PRs authored by dependabot.
2. Collect the latest version of each PR file from the commits ([GithubPR](/components/github-pr.md)).
3. Filter files by content and by path glob.
4. Find existing bot comments (headers `### AIxplain Comment` / `### AIxplain Summary`, hidden `<!--FILE:name|sha-->` and SHA markers); drop comments for changed files, re-review only new/changed files.
5. Generate comments via the abstract `_generate_comment`; discard those containing "All Good Here!".
6. Append a summary comment, delete deprecated comments, post new ones.

Prompts are chosen per file by glob (`*.py`, `*.md`, `*.*`, `*`) and include user instructions. Subclasses set `MAX_TOKENS`, used to truncate content.

Implementations: [ChatGPT](/components/chatgpt.md), [Google Gemini](/components/google-gemini.md).
