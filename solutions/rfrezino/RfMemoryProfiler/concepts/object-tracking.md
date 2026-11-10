---
type: "Mechanism"
title: "Object tracking"
description: "How class instances are counted by patching TObject.NewInstance and the VMT free slot."
---
# Object tracking

- `TObject.NewInstance` is redirected to `TObjectHack.NNewInstanceTrace`, which increments a per-class counter (`TRfClassController`, registered via `RegisterRfClassController`).
- The VMT `vmtFreeInstance` entry is replaced by `TRfObjectHack.NFreeInstance`, which decrements the counter then calls the original.
- Code patching uses `AddressPatch`/`PatchCodeDWORD`; the call-site address is captured via frame-based stack trace when `INSTANCES_TRACKER` is on, stored per caller in an [allocation map](/concepts/allocation-map.md).
- `RfGetInstanceList` returns the list of class controllers.

Controlled by [compile options](/concepts/compile-options.md).
