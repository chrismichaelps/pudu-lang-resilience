---
type: module
path: "@root/src/PuduLangResilience/Domain/Health.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, deep]
aliases: [PuduLangResilience.Domain.Health]
---

# PuduLangResilience.Domain.Health

## Purpose

Rolling success and failure counts over a sampling period, split into ten windows (one when shorter than 200 ms).

## Interface

### Signatures

```pudu
export type Window = { start: Int, successes: Int, failures: Int }

export type Health = { sampling: Int, windowLength: Int, windows: Array[Window] }

export type Info = { throughput: Int, failures: Int }

export fn create(sampling: Int) -> Health

export fn record(health: &Health, now: Int, failed: Bool) -> Health

export fn info(health: &Health, now: Int) -> Info

export fn reset(health: &Health) -> Health

export fn shouldBreak(totals: &Info, ratio: Decimal, minimumThroughput: Int) -> Bool

export fn failureRate(totals: &Info) -> Decimal
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Utils/Numeric]], `Std.Decimal`, `Std.List`, `Std.Math`, `Std.Option`.
- **Consumed by:** [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Domain/Circuit]].

## Algorithm

1. Recording rolls the windows: a new window once the current one is full, and none older than the period.
2. Totals sum the windows still inside the period.
3. A break is due when throughput reaches the minimum and `failures >= ratio * throughput`, compared exactly in decimal.

## Negative Logic (Prohibited Paths)

- No outcome older than the sampling period is counted.

## Edge Cases

- The failure rate is zero without throughput and rounded to six places.

## Depth

DEPTH 0.8 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why ten windows?
  **A:** Old outcomes leave in tenths of the period rather than all at once. _Rejected:_ one window (counts drop to zero at once).

## Referenced by

[[CHANGELOG]] · [[domain/Circuit]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Domain/Circuit]] · [[src/PuduLangResilience/Domain/_MOC]] · [[src/PuduLangResilience/Utils/Numeric]]
