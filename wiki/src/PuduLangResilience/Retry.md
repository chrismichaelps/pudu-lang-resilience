---
type: module
path: "@root/src/PuduLangResilience/Retry.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Retry]
---

# PuduLangResilience.Retry

## Purpose

Runs a callback again after outcomes its predicate handles, waiting a computed delay between attempts.

## Interface

### Signatures

```pudu
export type Backoff = Constant | Linear | Exponential

export type Retrying[T, E] = {
  outcome: Resilience.Outcome[T, E],
  context: Context.Context,
  attempt: Int,
  delay: Int,
  duration: Int
}

export type Options[T, E] = {
  name: Option[Str],
  shouldHandle: Predicate.Predicate[T, E],
  maxRetryAttempts: Int,
  backoff: Backoff,
  delay: Int,
  maxDelay: Option[Int],
  useJitter: Bool,
  delayGenerator: Option[fn(Predicate.Arguments[T, E]) -> Option[Int]],
  onRetry: Option[fn(Retrying[T, E]) -> ()]
}

export const UNLIMITED: Int = 9223372036854775807

export fn defaults[T, E]() -> Options[T, E]

export fn validate[T, E](options: &Options[T, E]) -> Array[Str]

export fn strategy[T, E](options: Options[T, E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Domain/Backoff]], [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience/Randomizer]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], `Std.Concurrent.Cancel`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Run, judge, report `ExecutionAttempt` (`Information` unhandled, `Warning` handled with retries left, `Error` on the last).
2. Stop on an unhandled outcome or after `maxRetryAttempts` retries; `UNLIMITED` never stops and stops counting.
3. Compute the delay with [[src/PuduLangResilience/Domain/Backoff]]; a generated delay of zero or more replaces it.
4. Report `OnRetry`, call `onRetry`, check the token, and wait on the pipeline clock; a fired token answers `Cancelled`.

## Negative Logic (Prohibited Paths)

- The callback never runs more than `maxRetryAttempts + 1` times.
- A cancellation is never retried by default.

## Edge Cases

- A zero delay does not wait but still checks the token.
- Options are refused when retries are below 1 or delays are outside 0 to one day.

## Depth

DEPTH 0.8 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Does `maxDelay` cap a generated delay?
  **A:** No: a generator is the caller's explicit choice. _Rejected:_ capping it (overrides an intentional value).
- **Q:** Why check the token before waiting rather than only during?
  **A:** A callback that cancelled its own token would otherwise pay one more wait. _Rejected:_ checking only inside the sleep.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Domain/Backoff]] · [[src/PuduLangResilience/Predicate]] · [[src/PuduLangResilience/Randomizer]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Strategies]]
