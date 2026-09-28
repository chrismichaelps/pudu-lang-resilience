---
type: module
path: "@root/src/PuduLangResilience/Limiter/SlidingWindow.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Limiter.SlidingWindow]
---

# PuduLangResilience.Limiter.SlidingWindow

## Purpose

A limiter granting at most `permitLimit` permits in any window, counted in segments.

## Interface

### Signatures

```pudu
export type Options = {
  permitLimit: Int,
  window: Int,
  segmentsPerWindow: Int,
  autoReplenishment: Bool,
  queueLimit: Int,
  queueOrder: Limiter.QueueOrder,
  clock: Clock.Clock
}

export fn defaults() -> Options

export fn validate(options: &Options) -> Array[Str]

export fn create(options: &Options) -> Result[Limiter.Limiter, Array[Str]]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Domain/Algorithms]], [[src/PuduLangResilience/Limiter/Engine]], [[src/PuduLangResilience/Limiter]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Validate, then build an engine over the sliding window algorithm with empty segments.

## Negative Logic (Prohibited Paths)

- A window shorter than its segment count is refused.

## Edge Cases

- A rejection names the time until enough old segments leave.

## Depth

DEPTH 0.4 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why segments?
  **A:** They bound memory while approximating a true sliding window. _Rejected:_ a log of every grant.

## Referenced by

[[src/PuduLangResilience/Limiter/_MOC]]
