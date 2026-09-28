---
type: module
path: "@root/src/PuduLangResilience/Hedging.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Hedging]
---

# PuduLangResilience.Hedging

## Purpose

Races extra attempts against a slow or failing one and answers the first acceptable outcome.

## Interface

### Signatures

```pudu
export type Action[T, E] = {
  primaryContext: Context.Context,
  actionContext: Context.Context,
  attempt: Int,
  callback: Strategy.Callback[T, E]
}

export type DelayArguments = { context: Context.Context, attempt: Int }

export type Hedged = { primaryContext: Context.Context, actionContext: Context.Context, attempt: Int }

export type Options[T, E] = {
  name: Option[Str],
  shouldHandle: Predicate.Predicate[T, E],
  maxHedgedAttempts: Int,
  delay: Int,
  actionGenerator: Option[fn(Action[T, E]) -> Option[Strategy.Callback[T, E]]],
  delayGenerator: Option[fn(DelayArguments) -> Int],
  onHedging: Option[fn(Hedged) -> ()]
}

export const WAIT_FOR_OUTCOME: Int = -1

export fn defaults[T, E]() -> Options[T, E]

export fn validate[T, E](options: &Options[T, E]) -> Array[Str]

export fn strategy[T, E](options: Options[T, E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Hedging/Attempts]], [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], `Std.Channel`, `Std.Concurrent.Cancel`, `Std.Option`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Start the primary. Wait for an attempt to finish for the attempt's delay (asked once per attempt); on timeout start the next attempt.
2. Judge each finished attempt and report `ExecutionAttempt`. An unhandled outcome wins. A handled one starts the next attempt at once.
3. When no further attempt can start and all have answered, the last to answer wins.
4. Cancel every other attempt, wait for each thread, and copy the winner's properties into the caller's context.

## Negative Logic (Prohibited Paths)

- No attempt's thread outlives the call.
- No more than `maxHedgedAttempts + 1` attempts run.

## Edge Cases

- A delay of 0 starts every attempt at once; `WAIT_FOR_OUTCOME` starts the next only after a handled outcome.
- An action generator answering nothing ends hedging.
- A crashed attempt is judged as `Crashed`.

## Depth

DEPTH 0.8 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why wait for losing attempts?
  **A:** Structured concurrency: a thread left running would hold resources past the call. _Rejected:_ returning at once and leaving losers running.
- **Q:** Why fork properties per attempt?
  **A:** Concurrent attempts must not overwrite each other's properties; only the winner's reach the caller. _Rejected:_ shared properties.

## Referenced by

[[src/PuduLangResilience/_MOC]]
