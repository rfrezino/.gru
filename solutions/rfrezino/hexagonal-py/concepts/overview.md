---
type: Project
title: hexagonal-py overview
description: Python tool enforcing hexagonal/onion layer dependency flow
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

`hexagonal-py` (v0.1.5, Poetry package, entry point `hexagonal`) checks that Python imports respect an ordered set of architecture layers, and can draw a diagram of them (via `diagrams`/Graphviz).

Code layout under `src/hexagonal`: `domain/` (entities), `services/` (builders, checker, importer), `use_cases/`, `infrastructure/cli.py`, and `hexagonal_config.py` (global config).

- [CLI](/concepts/cli.md)
- [Configuration](/concepts/configuration.md)
- [Layers and rules](/concepts/layers-and-rules.md)
