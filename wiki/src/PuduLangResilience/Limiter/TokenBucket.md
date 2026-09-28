---
type: module
path: "@root/src/PuduLangResilience/Limiter/TokenBucket.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Limiter.TokenBucket]
---

# PuduLangResilience.Limiter.TokenBucket

## Purpose

A limiter drawing permits from a bucket refilled by `tokensPerPeriod` every `replenishmentPeriod`, by itself or by hand.

## Interface

### Signatures

```pudu
export type Options = {
  tokenLimit: Int,
  tokensPerPeriod: Int,
  replenishmentPeriod: Int,
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

1. Validate, then build an engine over the token bucket algorithm starting full.

## Negative Logic (Prohibited Paths)

- The bucket never holds more than `tokenLimit`.

## Edge Cases

- A bucket replenished by hand gives no retry time.

## Depth

DEPTH 0.4 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why refill lazily?
  **A:** Computing whole periods at each request equals a timer without one. _Rejected:_ a refill thread.

## Referenced by

[[src/PuduLangResilience/Limiter/_MOC]]
