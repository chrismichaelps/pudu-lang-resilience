---
type: module
path: "@root/src/PuduLangResilience/Clock.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, seam]
aliases: [PuduLangResilience.Clock]
---

# PuduLangResilience.Clock

## Purpose

The monotonic time and cancellable waiting every strategy uses, as a record of functions so tests substitute a manual clock ([[seams/Runtime]]).

## Interface

### Signatures

```pudu
export type Clock = {
  now: fn() -> Int,
  sleep: fn(Int, Cancel.Token) -> Result[(), Cancel.Reason]
}

export type Manual = { time: Shared.Shared[Int], slept: Shared.Shared[Array[Int]] }

export fn system() -> Clock

export fn manual(start: Int) -> Manual

export fn ofManual(source: &Manual) -> Clock

export fn advance(target: &Manual, millis: Int) -> ()

export fn sleeps(source: &Manual) -> Array[Int]

export fn now(source: &Clock) -> Int

export fn sleep(source: &Clock, millis: Int, token: &Cancel.Token) -> Result[(), Cancel.Reason]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Utils/Numeric]], [[src/PuduLangResilience/Utils/Shared]], `Std.Concurrent.Cancel`, `Std.Sync`, `Std.Time`.
- **Consumed by:** [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Hedging/Attempts]], [[src/PuduLangResilience/Limiter/Concurrency]], [[src/PuduLangResilience/Limiter/Engine]], [[src/PuduLangResilience/Limiter/FixedWindow]], [[src/PuduLangResilience/Limiter/SlidingWindow]], [[src/PuduLangResilience/Limiter/TokenBucket]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Strategy]].

## Algorithm

1. `system` reads `Time.elapsed` and waits with `Cancel.pause`, which returns as soon as the token fires.
2. A manual clock holds its time and its requested waits in shared state; waiting checks the token, records the wait (negative as zero), and advances the time at once.

## Negative Logic (Prohibited Paths)

- A strategy never sleeps with `Std.Concurrent.sleep` directly.

## Edge Cases

- A fired token makes a manual sleep fail without recording it.
- `advance` ignores negative amounts.

## Depth

DEPTH 0.7 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why does a manual sleep advance time instead of blocking?
  **A:** Tests of retries, breaks, and windows become instant and exact; the recorded waits are the assertion. _Rejected:_ a manual clock that blocks until advanced (needs a second thread per test).

## Referenced by

[[seams/Runtime]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Hedging/Attempts]] · [[src/PuduLangResilience/Limiter/Concurrency]] · [[src/PuduLangResilience/Limiter/Engine]] · [[src/PuduLangResilience/Limiter/FixedWindow]] · [[src/PuduLangResilience/Limiter/SlidingWindow]] · [[src/PuduLangResilience/Limiter/TokenBucket]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Utils/Numeric]] · [[src/PuduLangResilience/Utils/Shared]] · [[src/PuduLangResilience/_MOC]]
