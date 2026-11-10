---
type: "Module"
title: "src/ai_assistent.py"
description: "Abstract base class implementing the review pipeline, prompts and comment bookkeeping."
---
`AiAssistent` (ABC) implements the [review workflow](/review-workflow.md); subclasses implement `_generate_comment(latest_file, instructions)` and set `MAX_TOKENS`. Also defines `LatestFile` and `FileInstructions` dataclasses, prompt templates (senior Python reviewer persona), `_sanitize_comment` and a rough token estimator.
