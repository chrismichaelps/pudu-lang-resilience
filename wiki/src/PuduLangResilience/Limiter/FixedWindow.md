---
type: module
path: "@root/src/PuduLangResilience/Limiter/FixedWindow.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Limiter.FixedWindow]
---

# PuduLangResilience.Limiter.FixedWindow

## Purpose

A limiter granting at most `permitLimit` permits per window.

## Interface

### Signatures

```pudu
export type Options = {
  permitLimit: Int,
  window: Int,
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

1. Validate, then build an engine over the fixed window algorithm whose first window starts now.

## Negative Logic (Prohibited Paths)

- No window grants more than its limit.

## Edge Cases

- A rejection names the time left in the window.

## Depth

DEPTH 0.4 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why align windows to the creation time?
  **A:** Windows then move in whole steps from a known start. _Rejected:_ wall-clock alignment (needs calendar time).

## Referenced by

[[src/PuduLangResilience/Limiter/_MOC]]
