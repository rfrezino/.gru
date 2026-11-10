---
type: "Project"
title: "AIxplain GitHub Action"
description: "Docker GitHub Action that uses ChatGPT or Google Gemini to review pull request files."
---
# Overview

A Docker-based GitHub Action. For each file changed in a PR it asks an LLM
(OpenAI ChatGPT if an OpenAI key is given, otherwise Google Gemini) for review
comments and posts them as PR comments. See [workflow](/review-workflow.md) and
[inputs](/action-inputs.md).

Python 3.11, PyGithub, `openai`, `google-genai`. Tests live in `tests/unit` and `tests/integration`.
