---
type: "Module"
title: "ChatGPT and GoogleGemini providers"
description: "Concrete AiAssistent implementations for OpenAI and Google Gemini."
---
- `ChatGPT` (src/chatgpt.py): MAX_TOKENS 4097, uses `openai`.
- `GoogleGemini` (src/google_gemini.py): MAX_TOKENS 10000, default model gemini-2.0-flash-001, location us-central1, uses `google.genai`.

Both extend [AiAssistent](/code/ai-assistent.md). Tests: `tests/unit/test_chatgpt.py`, `tests/unit/test_ai_assistent.py`, `tests/integration/test_chatgpt.py`.
