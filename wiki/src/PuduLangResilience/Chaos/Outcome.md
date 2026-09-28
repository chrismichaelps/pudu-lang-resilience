---
type: module
path: "@root/src/PuduLangResilience/Chaos/Outcome.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Chaos.Outcome]
---

# PuduLangResilience.Chaos.Outcome

## Purpose

Answers a chosen outcome instead of running the callback, for a share of executions.

## Interface

### Signatures

```pudu
export type Injected[T, E] = { context: Context.Context, outcome: Resilience.Outcome[T, E] }

export type Options[T, E] = {
  name: Option[Str],
  injection: Chaos.Injection,
  outcome: Option[Resilience.Outcome[T, E]],
  generator: Option[fn(Chaos.Arguments) -> Option[Resilience.Outcome[T, E]]],
  onInjected: Option[fn(Injected[T, E]) -> ()]
}

export fn defaults[T, E]() -> Options[T, E]

export fn injecting[T, E](outcome: Resilience.Outcome[T, E], injection: Chaos.Injection) -> Options[T, E]

export fn validate[T, E](options: &Options[T, E]) -> Array[Str]

export fn strategy[T, E](options: Options[T, E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. When injecting, take the generator's outcome or the fixed one; report `Chaos.OnOutcome`, call `onInjected`, and answer it.

## Negative Logic (Prohibited Paths)

- An injected outcome never runs the callback.

## Edge Cases

- A generator answering nothing injects nothing.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why separate from faults?
  **A:** An outcome may be a value, which a fault cannot be. _Rejected:_ one strategy for both.

## Referenced by

[[src/PuduLangResilience/Chaos/_MOC]]
