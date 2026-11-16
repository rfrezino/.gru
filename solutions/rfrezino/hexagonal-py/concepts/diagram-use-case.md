---
type: Process
title: Generate diagram
description: Builds a Graphviz diagram of layers
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

`GenerateDiagramUseCase.execute` draws one `Cluster` per layer using `diagrams`, a `Storage` node per directory, linking layers in order; layers with several directory groups get nested clusters. Returns whether any node was drawn. Used by the `diagram` command in the [CLI](/concepts/cli.md).
