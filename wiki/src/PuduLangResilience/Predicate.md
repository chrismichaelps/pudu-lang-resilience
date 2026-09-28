---
type: module
path: "@root/src/PuduLangResilience/Predicate.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Predicate]
---

# PuduLangResilience.Predicate

## Purpose

The shared shape of every reactive strategy's `shouldHandle`: which outcomes, errors, values, or rejections a strategy acts on.

## Interface

### Signatures

```pudu
export type Arguments[T, E] = { outcome: Resilience.Outcome[T, E], context: Context.Context, attempt: Int }

export type Predicate[T, E] = fn(Arguments[T, E]) -> Bool

export fn arguments[T, E](outcome: Resilience.Outcome[T, E], context: Context.Context, attempt: Int) -> Arguments[T, E]

export fn handles[T, E](test: &Predicate[T, E], given: &Arguments[T, E]) -> Bool

export fn failures[T, E]() -> Predicate[T, E]

export fn raised[T, E](test: fn(E) -> Bool) -> Predicate[T, E]

export fn anyRaised[T, E]() -> Predicate[T, E]

export fn failure[T, E](test: fn(Resilience.Failure[E]) -> Bool) -> Predicate[T, E]

export fn results[T, E](test: fn(T) -> Bool) -> Predicate[T, E]

export fn result[T, E](expected: T) -> Predicate[T, E]

export fn timeouts[T, E]() -> Predicate[T, E]

export fn brokenCircuits[T, E]() -> Predicate[T, E]

export fn rateLimited[T, E]() -> Predicate[T, E]

export fn anyOf[T, E](tests: &Array[Predicate[T, E]]) -> Predicate[T, E]

export fn never[T, E]() -> Predicate[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]].
- **Consumed by:** [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Retry]].

## Algorithm

1. `Arguments` hold the outcome, the context, and the attempt number (0 outside retry and hedging).
2. Constructors each test one kind of outcome; `anyOf` handles what any of its parts handles.

## Negative Logic (Prohibited Paths)

- The default, `failures`, never handles a cancellation or a value.

## Edge Cases

- `anyOf` of nothing handles nothing.

## Depth

DEPTH 0.7 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why an array for combination instead of a builder chain?
  **A:** The call site reads flat as one list of conditions. _Rejected:_ chained builder methods (nesting-forcing).

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Strategies]]
