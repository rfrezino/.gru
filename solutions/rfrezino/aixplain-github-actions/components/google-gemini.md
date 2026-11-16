---
tags: [aixplain]
type: Module
title: Google Gemini provider
description: AiAssistent implementation using Gemini through Vertex AI.
resource: src/google_gemini.py
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
Subclass of [AiAssistent](/components/ai-assistent.md) with `MAX_TOKENS = 10000`. Creates a `genai.Client(vertexai=True)` with the configured project and location (default `us-central1`) and model (default gemini-2.0-flash-001). Prompts as a code reviewer with the file content.
