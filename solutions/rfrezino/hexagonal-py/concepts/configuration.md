---
type: Configuration
title: Configuring layers
description: TOML or Python configuration of layers and excluded dirs
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

Two sources, TOML preferred:

1. `[tool.hexagonalpy]` in `pyproject.toml` (`TomlConfiguration`): `excluded_dirs`, and `[tool.hexagonalpy.layer.N]` tables with `name` and `directories_groups`. Layer 1 is innermost; keys are sorted so the layer order follows N.
2. `hexagonal_config.py` in the source folder, executed with `runpy`, calling `hexagonal_config.add_inner_layer_with_dirs(...)` from outermost to innermost and setting `excluded_dirs`.

Directory entries must start with `/` and not end with `/` (validated in `HexagonalLayer`). Files under dot-directories are ignored; excluded dirs match by path prefix relative to the source folder.

See [Layers and rules](/concepts/layers-and-rules.md).
