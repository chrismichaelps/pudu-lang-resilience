---
type: module
path: "@root/src/PuduLangResilience/Limiter/Concurrency.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Limiter.Concurrency]
---

# PuduLangResilience.Limiter.Concurrency

## Purpose

A limiter holding at most `permitLimit` executions at once, with a queue.

## Interface

### Signatures

```pudu
export type Options = { permitLimit: Int, queueLimit: Int, queueOrder: Limiter.QueueOrder, clock: Clock.Clock }

export fn defaults() -> Options

export fn validate(options: &Options) -> Array[Str]

export fn create(options: &Options) -> Result[Limiter.Limiter, Array[Str]]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Domain/Algorithms]], [[src/PuduLangResilience/Limiter/Engine]], [[src/PuduLangResilience/Limiter]].
- **Consumed by:** [[src/PuduLangResilience/RateLimiter]].

## Algorithm

1. Validate, then build an engine over the concurrency algorithm starting with every permit available.

## Negative Logic (Prohibited Paths)

- No more than `permitLimit` permits are ever held.

## Edge Cases

- A queue of 0 denies at once when no permit is free.

## Depth

DEPTH 0.4 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why does it take a clock?
  **A:** Queued requests wait on it. _Rejected:_ a hidden system clock.

## Referenced by

[[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Domain/Algorithms]] · [[src/PuduLangResilience/Limiter]] · [[src/PuduLangResilience/Limiter/Engine]] · [[src/PuduLangResilience/Limiter/_MOC]] · [[src/PuduLangResilience/RateLimiter]]
