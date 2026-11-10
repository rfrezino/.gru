---
type: "Concept"
title: "Configuration"
description: "How layers and excluded dirs are configured"
---
# Sources
1. `pyproject.toml` under `[tool.hexagonalpy]` (preferred, read by [TomlConfiguration](/components/config-loading.md)): `excluded_dirs`, and `[tool.hexagonalpy.layer.N]` with `name` and `directories_groups` (list of lists). N=1 is innermost.
2. `hexagonal_config.py` in the source folder, calling `hexagonal_config.add_inner_layer_with_dirs(...)` from outermost to innermost.

Directories must start with `/` and not end with `/` (validated by `HexagonalLayer`). TOML wins when present. Rule: [layer rule](/concepts/layer-dependency-rule.md).
