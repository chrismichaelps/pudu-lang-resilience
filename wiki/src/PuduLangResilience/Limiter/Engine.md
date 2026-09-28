---
type: module
path: "@root/src/PuduLangResilience/Limiter/Engine.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.75
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Limiter.Engine]
---

# PuduLangResilience.Limiter.Engine

## Purpose

Turns a permit algorithm into a thread-safe limiter with a wait queue.

## Interface

### Signatures

```pudu
export type Queueing = { permitLimit: Int, queueLimit: Int, order: Limiter.QueueOrder }

export fn limiter[S](held: S, algorithm: Permits.Algorithm[S], queueing: &Queueing, clock: Clock.Clock) -> Limiter.Limiter
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Domain/Permits]], [[src/PuduLangResilience/Limiter]], [[src/PuduLangResilience/Utils/Shared]], `Std.Concurrent.Cancel`.
- **Consumed by:** [[src/PuduLangResilience/Limiter/Concurrency]], [[src/PuduLangResilience/Limiter/FixedWindow]], [[src/PuduLangResilience/Limiter/SlidingWindow]], [[src/PuduLangResilience/Limiter/TokenBucket]].

## Algorithm

1. Every request and turn is decided by [[src/PuduLangResilience/Domain/Permits]] under the book's lock.
2. A queued request asks again every millisecond on the limiter's clock; a fired token withdraws it and answers `Abandoned`.
3. A granted lease hands its permits back to the book on release.

## Negative Logic (Prohibited Paths)

- A waiter that was evicted or withdrawn never takes permits.

## Edge Cases

- A request with a fired token is abandoned before it queues.

## Depth

DEPTH 0.75 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why polling rather than wake-ups?
  **A:** Permits return by time as well as by release; one polling loop serves both, and a manual clock drives it in tests. _Rejected:_ condition signals per release (misses time-based refills).

## Referenced by

[[src/PuduLangResilience/Limiter/_MOC]] · [[src/PuduLangResilience/Limiter/Concurrency]] · [[src/PuduLangResilience/Limiter/FixedWindow]] · [[src/PuduLangResilience/Limiter/SlidingWindow]] · [[src/PuduLangResilience/Limiter/TokenBucket]]
