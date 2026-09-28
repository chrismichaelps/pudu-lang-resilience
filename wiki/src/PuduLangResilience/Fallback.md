---
type: module
path: "@root/src/PuduLangResilience/Fallback.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Fallback]
---

# PuduLangResilience.Fallback

## Purpose

Answers a substitute outcome in place of one its predicate handles.

## Interface

### Signatures

```pudu
export type Options[T, E] = {
  name: Option[Str],
  shouldHandle: Predicate.Predicate[T, E],
  action: Option[fn(Predicate.Arguments[T, E]) -> Resilience.Outcome[T, E]],
  onFallback: Option[fn(Predicate.Arguments[T, E]) -> ()]
}

export fn defaults[T, E]() -> Options[T, E]

export fn withValue[T, E](value: T) -> Options[T, E]

export fn validate[T, E](options: &Options[T, E]) -> Array[Str]

export fn strategy[T, E](options: Options[T, E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Run; when handled, report `OnFallback`, call `onFallback`, and answer the action's outcome.

## Negative Logic (Prohibited Paths)

- No fallback without an action can be built.

## Edge Cases

- A failing substitute is answered as it is.
- A value may be handled by a result predicate and replaced.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why is the action optional in the type?
  **A:** Defaults must exist for record updates; validation refuses the missing action. _Rejected:_ a required constructor argument (breaks the options-record pattern).

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Predicate]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Strategies]]
