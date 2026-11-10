---
type: "Types"
title: "Header types"
description: "Shared type aliases and enums."
---
- `TDataDogTag`, `TDataDogAspect`: strings; `TDataDogTags` = array of tag.
- `TDataDogEventPriority`: ddLow, ddNormal.
- `TDataDogEventAlertType`: ddError, ddWarning, ddInfo, ddSuccess, ddUndefined.
- `TDataDogServiceStatus`: dssOK=0, dssWarning=1, dssCritical=2, dssUnknown=3, dssUndefined=4.

Used by [event](/components/event.md), [service check](/components/service-check.md) and [utils](/components/utils.md).
