---
type: "Design Notes"
title: "Parking lot design"
description: "OOP design notes for the Google/Amazon parking lot question"
---

# Notes

Source: `playground/concepts/concept_1_parking_lot/README.md`.

- Know creational, structural and behavioral design patterns; top-down vs bottom-up (OO) design.
- Do not use an Enum for spot type: it violates Open/Closed; use subclasses.

Code: `parking-lot-oop-old/src` with `domain/parking_lot` (parking_lot, parking_level, level_row, spot), `domain/vehicles` (vehicle, bus, compact_car, motor_cycle, vehicle_type) and `application/main.py`. See [playground code](/code/playground.md).
