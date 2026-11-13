---
type: Decision
title: Open questions and risks
description: Unresolved product risks around AI chat moderation, historical member accuracy and affiliate legality.
tags: [risk, ai, legal]
status: draft
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: readme, resource: /README.md }
---

* **AI chat moderation.** Google's Natural Language `moderateText` and Vertex safety filters detect toxicity, not theological sensitivity; custom guardrails are needed. Perspective API shuts down 2026-12-31 so do not adopt it. Any paid service needs a procurement ticket.[^readme]
* **Historical member accuracy.** Proposed: a second independent AI pass fact-checks the draft before human publish approval.
* **Affiliate legal review.** Brazil first (disclosure, tax, consumer protection), with a repeatable process for new countries.

[^readme]: Project vision README
