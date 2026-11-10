---
type: "Data Structure"
title: "TAllocationMap"
description: "Sorted list of caller addresses with allocation counters."
---
# TAllocationMap

Stores `TCallerAllocator` entries (caller address + counter). `FindOrAdd` uses binary search (`BinarySearch`) over a list kept ordered by `QuickSort`; `IncCounter`/`DecCounter` update counts; guarded by a critical section. Used by [object](/concepts/object-tracking.md) and buffer tracking to group by caller.
