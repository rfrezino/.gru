---
tags: [aixplain]
type: Module
title: ChatGPT provider
description: AiAssistent implementation using the OpenAI API.
resource: src/chatgpt.py
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
Subclass of [AiAssistent](/components/ai-assistent.md) with `MAX_TOKENS = 4097`. Sets `openai.api_key` and sends the whole file plus the git-diff patch to the model; on content errors it posts an error comment.
