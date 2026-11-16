---
type: Test Suite
title: DUnit tests
description: Test project verifying counters.
tags: [tests, dunit]
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: dpr, resource: "RfMemoryProfilerTest.dpr", title: "Test program" }
---
# Tests
`RfMemoryProfilerTest.dpr` runs registered DUnit tests (GUI or console runner); requires the UNITTEST project directive. Cases live in `TestuRfMemoryProfiler.pas`, with fixtures in `uUnitTestHeader.pas`. They exercise the counters of the [public API](/concepts/public-api.md).
