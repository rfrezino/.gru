---
type: "Module"
title: "src/execute.py"
description: "Chooses ChatGPT or Gemini and runs the review."
---
Builds a [GithubPR](/code/github-pr.md), splits `;` lists, then uses [ChatGPT](/code/providers.md) if the OpenAI token is non-empty, else Gemini. Raises ValueError only when both tokens are `None`.
