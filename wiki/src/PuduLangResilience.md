---
type: module
path: "@root/src/PuduLangResilience.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, backbone]
aliases: [PuduLangResilience]
---

# PuduLangResilience

## Purpose

The package root and its vocabulary: the `Failure` a guarded execution answers instead of a value, the `Outcome` every callback and strategy answers, and the helpers that build, inspect, and describe them.

## Interface

### Signatures

```pudu
export type Failure[E]
  = Raised(E)
  | TimedOut(Int)
  | BrokenCircuit(Int)
  | IsolatedCircuit
  | RateLimited(Option[Int])
  | Cancelled(Str)
  | Crashed(Str)

export type Outcome[T, E] = Result[T, Failure[E]]

export fn succeed[T, E](value: T) -> Outcome[T, E]

export fn raise[T, E](problem: E) -> Outcome[T, E]

export fn lift[T, E](result: Result[T, E]) -> Outcome[T, E]

export fn raised[E](failure: &Failure[E]) -> Option[E]

export fn isCancellation[E](failure: &Failure[E]) -> Bool

export fn isRejection[E](failure: &Failure[E]) -> Bool

export fn describe[E](failure: &Failure[E]) -> Str

export fn summarize[T, E](outcome: &Outcome[T, E]) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Constants/Messages]], [[src/PuduLangResilience/Utils/Template]].
- **Consumed by:** [[src/PuduLangResilience/Chaos/Behavior]], [[src/PuduLangResilience/Chaos/Fault]], [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/Chaos/Outcome]], [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Hedging/Attempts]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience/RateLimiter]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Timeout]].

## Algorithm

1. `lift` maps a plain `Result` onto an outcome: `Ok` stays, `Err(e)` becomes `Raised(e)`.
2. `isRejection` is true for the four variants a proactive or circuit strategy produces: `TimedOut`, `BrokenCircuit`, `IsolatedCircuit`, `RateLimited`.
3. `describe` fills the sentence for each variant from [[src/PuduLangResilience/Constants/Messages]]; `summarize` renders a value as `Ok(<value>)` and a failure as its description.

## Negative Logic (Prohibited Paths)

- No wording is written inline; every sentence comes from the message constants.
- No variant carries an exception-like payload beyond what the caller needs to act: a duration, a retry-after, or a reason.

## Edge Cases

- `RateLimited(None)` has its own sentence; a limiter that cannot tell the wait says so rather than inventing one.
- `Raised` shows its error with `show`, so text errors appear quoted.

## Depth

DEPTH 0.8 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why one generic `Failure[E]` instead of the callback's own error type?
  **A:** Strategies must add their own rejections (timeout, open circuit, rate limit, cancellation) without knowing `E`; one sum type carries both. _Rejected:_ a trait object of errors (loses exhaustive matching); separate result types per strategy (callers would unwrap one per layer).
- **Q:** Why is cancellation not a rejection?
  **A:** Cancellation comes from the caller's token, not from a policy; the default predicates exclude it so a caller that asked to stop is never retried or substituted. _Rejected:_ treating it as a rejection (a cancelled call would be retried).
- **Q:** Why `Crashed`?
  **A:** Hedged attempts run on threads; a thread that stops is reported as a value instead of taking the call down. _Rejected:_ aborting the caller.

## Referenced by

[[domain/Outcome]] · [[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Constants/Messages]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Hedging/Attempts]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Predicate]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Timeout]] · [[src/PuduLangResilience/Utils/Template]] · [[src/_MOC]]
