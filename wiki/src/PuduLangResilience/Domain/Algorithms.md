---
type: module
path: "@root/src/PuduLangResilience/Domain/Algorithms.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.75
depth_status: DEEP
tags: [module, deep]
aliases: [PuduLangResilience.Domain.Algorithms]
---

# PuduLangResilience.Domain.Algorithms

## Purpose

How each limiter kind counts: concurrency, token bucket, fixed window, and sliding window.

## Interface

### Signatures

```pudu
export type Bucket = { tokens: Int, stamp: Int }

export type Window = { used: Int, start: Int }

export type Segments = { counts: Array[Int], start: Int }

export fn concurrency(limit: Int) -> Permits.Algorithm[Int]

export fn tokenBucket(limit: Int, perPeriod: Int, period: Int, automatic: Bool) -> Permits.Algorithm[Bucket]

export fn fixedWindow(limit: Int, window: Int, automatic: Bool) -> Permits.Algorithm[Window]

export fn slidingWindow(limit: Int, window: Int, segments: Int, automatic: Bool) -> Permits.Algorithm[Segments]

export fn shifted(counts: &Array[Int], steps: Int) -> Array[Int]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Domain/Permits]], [[src/PuduLangResilience/Utils/Numeric]], `Std.Math`.
- **Consumed by:** [[src/PuduLangResilience/Limiter/Concurrency]], [[src/PuduLangResilience/Limiter/FixedWindow]], [[src/PuduLangResilience/Limiter/SlidingWindow]], [[src/PuduLangResilience/Limiter/TokenBucket]].

## Algorithm

1. Concurrency: permits leave on grant and return on release.
2. Token bucket: whole periods since the last refill add tokens up to the limit; the retry time is the periods needed less the time already passed.
3. Fixed window: whole windows since the start reset the count; the retry time is the rest of the window.
4. Sliding window: whole segments since the start shift out the oldest counts; the retry time is when enough old segments leave.

## Negative Logic (Prohibited Paths)

- Manual replenishment kinds never refill with time and give no retry time.

## Edge Cases

- Shifting past every segment empties them.

## Depth

DEPTH 0.75 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why one module for four algorithms?
  **A:** Each is a handful of pure functions sharing the same record shape. _Rejected:_ four shallow modules.

## Referenced by

[[src/PuduLangResilience/Domain/Permits]] · [[src/PuduLangResilience/Domain/_MOC]] · [[src/PuduLangResilience/Limiter/Concurrency]] · [[src/PuduLangResilience/Limiter/FixedWindow]] · [[src/PuduLangResilience/Limiter/SlidingWindow]] · [[src/PuduLangResilience/Limiter/TokenBucket]] · [[src/PuduLangResilience/Utils/Numeric]] · [[subsystems/Limiting]]
