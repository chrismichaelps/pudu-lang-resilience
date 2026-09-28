---
type: module
path: "@root/src/PuduLangResilience/Limiter.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.65
depth_status: MEDIUM
tags: [module, seam]
aliases: [PuduLangResilience.Limiter]
---

# PuduLangResilience.Limiter

## Purpose

The contract every limiter answers: acquire with waiting, attempt without, statistics, and replenishment by hand; leases and chaining.

## Interface

### Signatures

```pudu
export type QueueOrder = OldestFirst | NewestFirst

export type Grant = Granted | Denied(Option[Int]) | Abandoned(Str)

export type Lease = { grant: Grant, release: fn() -> () }

export type Statistics = { available: Int, queued: Int, failed: Int, succeeded: Int }

export type Limiter = {
  acquire: fn(Int, Cancel.Token) -> Lease,
  attempt: fn(Int) -> Lease,
  statistics: fn() -> Statistics,
  replenish: fn() -> Bool
}

export fn acquire(limiter: &Limiter, permits: Int, token: &Cancel.Token) -> Lease

export fn attempt(limiter: &Limiter, permits: Int) -> Lease

export fn statistics(limiter: &Limiter) -> Statistics

export fn replenish(limiter: &Limiter) -> Bool

export fn isGranted(lease: &Lease) -> Bool

export fn release(lease: &Lease) -> ()

export fn refused(grant: Grant) -> Lease

export fn granted(handBack: fn() -> ()) -> Lease

export fn chain(limiters: Array[Limiter]) -> Limiter
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Utils/Shared]], `Std.Concurrent.Cancel`.
- **Consumed by:** [[src/PuduLangResilience/Limiter/Concurrency]], [[src/PuduLangResilience/Limiter/Engine]], [[src/PuduLangResilience/Limiter/FixedWindow]], [[src/PuduLangResilience/Limiter/Partitioned]], [[src/PuduLangResilience/Limiter/SlidingWindow]], [[src/PuduLangResilience/Limiter/TokenBucket]], [[src/PuduLangResilience/RateLimiter]].

## Algorithm

1. A `Lease` is `Granted`, `Denied(retryAfter)`, or `Abandoned(reason)`, with a release that runs once.
2. `chain` acquires from each limiter in order and releases what it took at the first refusal.

## Negative Logic (Prohibited Paths)

- Releasing a lease twice returns its permits once.

## Edge Cases

- A chain reports the smallest availability and the summed counts.

## Depth

DEPTH 0.65 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why a record of functions?
  **A:** Callers may bring their own limiter; the strategy only needs these four operations. _Rejected:_ a trait (a record is simpler to build from closures).

## Referenced by

[[src/PuduLangResilience/_MOC]] · [[src/PuduLangResilience/Limiter/Concurrency]] · [[src/PuduLangResilience/Limiter/Engine]] · [[src/PuduLangResilience/Limiter/FixedWindow]] · [[src/PuduLangResilience/Limiter/Partitioned]] · [[src/PuduLangResilience/Limiter/SlidingWindow]] · [[src/PuduLangResilience/Limiter/TokenBucket]] · [[src/PuduLangResilience/RateLimiter]]
