---
type: "Component"
title: "HexagonalDependencyFlowChecker"
description: "Detects inner-to-outer and cross-group imports"
---
For each file in each layer, each imported module that belongs to a layer is compared: inner layer index < imported layer index means a wrong-flow error; same layer but different directory group means a group inter-dependency error. Implements the [layer rule](/concepts/layer-dependency-rule.md).
