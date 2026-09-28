---
type: module
path: "@root/src/PuduLangResilience/Telemetry/Meter.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Telemetry.Meter]
---

# PuduLangResilience.Telemetry.Meter

## Purpose

A listener that counts events by series and records attempt and execution durations.

## Interface

### Signatures

```pudu
export type Meter = {
  counts: Shared.Shared[Map[Str, Int]],
  durations: Shared.Shared[Map[Str, Array[Int]]],
  enrich: fn(Telemetry.Event) -> Array[(Str, Str)]
}

export fn create() -> Meter

export fn enriched(enrich: fn(Telemetry.Event) -> Array[(Str, Str)]) -> Meter

export fn listener(meter: &Meter) -> Telemetry.Listener

export fn count(meter: &Meter, name: Str) -> Int

export fn series(meter: &Meter) -> Array[(Str, Int)]

export fn durations(meter: &Meter, name: Str) -> Array[Int]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Telemetry]], [[src/PuduLangResilience/Utils/Shared]], `Std.Map`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. A series is the event name, `source=` its source, and every tag the enricher adds.
2. `count` sums every series of one event name.

## Negative Logic (Prohibited Paths)

- Only `Attempted` and `Executed` details record a duration.

## Edge Cases

- An unknown event name counts zero and has no durations.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why a string series key?
  **A:** It prints as it is stored and needs no nested maps. _Rejected:_ a map of maps per tag.

## Referenced by

[[src/PuduLangResilience/Telemetry/_MOC]]
