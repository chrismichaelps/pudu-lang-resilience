---
type: module
path: "@root/src/PuduLangResilience/Telemetry/Log.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Telemetry.Log]
---

# PuduLangResilience.Telemetry.Log

## Purpose

A listener writing each event at or above a severity as one line.

## Interface

### Signatures

```pudu
export fn listener(least: Telemetry.Severity, write: fn(Str) -> ()) -> Telemetry.Listener

export fn format(event: &Telemetry.Event) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. `[Severity] Name source=… operation=… <detail> outcome=…`, leaving out the parts an event lacks.

## Negative Logic (Prohibited Paths)

- The listener never writes below its least severity.

## Edge Cases

- Details without measurements add nothing to the line.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why a write function instead of stdout?
  **A:** The caller chooses the stream or logger. _Rejected:_ writing to stderr unconditionally.

## Referenced by

[[src/PuduLangResilience/Telemetry/_MOC]]
