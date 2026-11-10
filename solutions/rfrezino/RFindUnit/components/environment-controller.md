---
type: "Component"
title: "Environment Controller"
description: "Owns library-path and project-path unit indexes."
---
# Environment Controller

`TEnvironmentController` (`Source/FindUnit.EnvironmentController.pas`, interface `Interf.EnvironmentController`) implements IOTAProjectFileStorageNotifier. It loads library path (`LoadLibraryPath`) and project path (`LoadProjectPath`) units through the [Parser Worker](/components/parser-worker.md), reacts to project load/save/close, exposes `GetProjectUnits`, `GetLibraryPathUnits`, `GetFullMatch`, `PasExists`, readiness and status flags, and `ProcessDCUFiles`. Results are held in [Units Cache](/components/units-cache.md) objects.
