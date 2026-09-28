---
type: module
path: "@root/src/PuduLangResilience/Utils/Shared.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, leaf]
aliases: [PuduLangResilience.Utils.Shared]
---

# PuduLangResilience.Utils.Shared

## Purpose

A value shared between threads that changes only under its lock, so every read-modify-write is atomic.

## Interface

### Signatures

```pudu
export type Shared[S] = { lock: Sync.Mutex, held: Sync.Cell[S] }

export fn shared[S](initial: S) -> Shared[S]

export fn current[S](source: &Shared[S]) -> S

export fn change[S, R](target: &Shared[S], step: fn(S) -> (S, R)) -> R

export fn update[S](target: &Shared[S], step: fn(S) -> S) -> ()
```

### Linkage

- **Requires:** `Std.Sync`.
- **Consumed by:** [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Hedging/Attempts]], [[src/PuduLangResilience/Limiter]], [[src/PuduLangResilience/Limiter/Engine]], [[src/PuduLangResilience/Limiter/Partitioned]], [[src/PuduLangResilience/Randomizer]], [[src/PuduLangResilience/Registry]], [[src/PuduLangResilience/Telemetry/Meter]].

## Algorithm

1. `change` takes the lock, reads, applies the step, stores its new value, and answers its result.
2. A lock or cell that cannot be reached stops the program with `panic`: the package created it, so its absence is a broken invariant.

## Negative Logic (Prohibited Paths)

- No module writes a `Sync.Cell` whose new value depends on its old one without going through `change`.

## Edge Cases

- The step runs under the lock; it must not call back into the same shared value.

## Depth

DEPTH 0.7 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why panic on a sync failure?
  **A:** The only failure `Std.Sync` reports is a missing handle, which cannot happen for a handle this module made; threading `Result` through every counter would hide real failures in noise. _Rejected:_ a fallback value (silently wrong counts).

## Referenced by

[[src/PuduLangResilience/Utils/_MOC]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Hedging/Attempts]] · [[src/PuduLangResilience/Limiter]] · [[src/PuduLangResilience/Limiter/Engine]] · [[src/PuduLangResilience/Limiter/Partitioned]] · [[src/PuduLangResilience/Randomizer]] · [[src/PuduLangResilience/Registry]] · [[src/PuduLangResilience/Telemetry/Meter]]
