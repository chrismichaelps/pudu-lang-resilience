---
type: module
path: "@root/src/PuduLangResilience/Hedging/Attempts.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Hedging.Attempts]
---

# PuduLangResilience.Hedging.Attempts

## Purpose

Runs one hedged attempt on its own thread, stores its outcome, and signals completion; waits for the next finished attempt; ends all attempts.

## Interface

### Signatures

```pudu
export type Attempt[T, E] = {
  index: Int,
  context: Context.Context,
  started: Int,
  outcome: Shared.Shared[Option[Resilience.Outcome[T, E]]],
  worker: Option[Concurrent.Task]
}

export const FOREVER: Int = -1

export fn launch[T, E](index: Int, context: Context.Context, callback: fn(Context.Context) -> Resilience.Outcome[T, E], finished: &Channel.Channel[Int], started: Int) -> Attempt[T, E]

export fn next(finished: &Channel.Channel[Int], clock: &Clock.Clock, wait: Int) -> Option[Int]

export fn outcomeOf[T, E](attempt: &Attempt[T, E]) -> Resilience.Outcome[T, E]

export fn settle[T, E](attempts: &Array[Attempt[T, E]], why: Str) -> ()
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Utils/Shared]], `Std.Channel`, `Std.Concurrent`, `Std.Concurrent.Cancel`.
- **Consumed by:** [[src/PuduLangResilience/Hedging]].

## Algorithm

1. An attempt runs inside `Concurrent.contain` on a thread; its outcome, or `Crashed`, is stored before its index is sent to the completion channel.
2. `next` waits for the channel with a bound, polling on the pipeline clock, or without one for `FOREVER`.
3. `settle` cancels every attempt's token and joins every thread.

## Negative Logic (Prohibited Paths)

- Every attempt signals completion exactly once, even when it cannot start.

## Edge Cases

- An attempt whose thread cannot start finishes at once as `Crashed`.

## Depth

DEPTH 0.7 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why poll the channel for a bounded wait?
  **A:** `Std.Channel` has no timed receive; polling the pending count on the pipeline clock keeps the wait cancellable and testable. _Rejected:_ a timer thread per wait.

## Referenced by

[[src/PuduLangResilience]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Hedging/_MOC]] · [[src/PuduLangResilience/Utils/Shared]]
