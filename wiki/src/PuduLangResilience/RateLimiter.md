---
type: module
path: "@root/src/PuduLangResilience/RateLimiter.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.RateLimiter]
---

# PuduLangResilience.RateLimiter

## Purpose

Admits an execution only when a limiter grants it a permit, and returns the permit when the execution ends.

## Interface

### Signatures

```pudu
export type Rejected = { context: Context.Context, retryAfter: Option[Int] }

export type Options = {
  name: Option[Str],
  acquire: Option[fn(Context.Context) -> Limiter.Lease],
  defaultLimiter: Concurrency.Options,
  onRejected: Option[fn(Rejected) -> ()]
}

export fn defaults() -> Options

export fn concurrency(permitLimit: Int, queueLimit: Int) -> Options

export fn using(limiter: Limiter.Limiter) -> Options

export fn partitioned(limiters: Partitioned.Partitioned) -> Options

export fn strategy[T, E](options: Options) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Limiter/Concurrency]], [[src/PuduLangResilience/Limiter]], [[src/PuduLangResilience/Limiter/Partitioned]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. The lease comes from the caller's `acquire`, or from a concurrency limiter built from `defaultLimiter` when the strategy is made.
2. Granted: run and release. Denied: report `OnRateLimiterRejected`, call `onRejected`, and answer `RateLimited(retryAfter)`. Abandoned: answer `Cancelled`.

## Negative Logic (Prohibited Paths)

- A granted permit is always released, whatever the callback answers.

## Edge Cases

- Invalid default limiter options are validation problems of the strategy.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why build the default limiter when the strategy is made?
  **A:** Every pipeline holding the strategy shares one limit, the expected meaning of a limit. _Rejected:_ one limiter per pipeline build.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Limiter]] · [[src/PuduLangResilience/Limiter/Concurrency]] · [[src/PuduLangResilience/Limiter/Partitioned]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Limiting]] · [[subsystems/Strategies]]
