---
type: module
path: "@root/src/PuduLangResilience/Chaos/Behavior.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Chaos.Behavior]
---

# PuduLangResilience.Chaos.Behavior

## Purpose

Runs an extra action before the callback, for a share of executions.

## Interface

### Signatures

```pudu
export type Options[E] = {
  name: Option[Str],
  injection: Chaos.Injection,
  behavior: Option[fn(Chaos.Arguments) -> Result[(), Resilience.Failure[E]]],
  onInjected: Option[fn(Context.Context) -> ()]
}

export fn defaults[E]() -> Options[E]

export fn injecting[E](behavior: fn(Chaos.Arguments) -> Result[(), Resilience.Failure[E]], injection: Chaos.Injection) -> Options[E]

export fn validate[E](options: &Options[E]) -> Array[Str]

export fn strategy[T, E](options: Options[E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. When injecting, report `Chaos.OnBehavior` and run the behavior; its failure is answered, otherwise call `onInjected` and run the callback.

## Negative Logic (Prohibited Paths)

- A failing behavior never runs the callback.

## Edge Cases

- A behavior is required.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why may a behavior fail?
  **A:** Injected behavior such as a dropped connection must be able to end the call. _Rejected:_ side effects only.

## Referenced by

[[src/PuduLangResilience/Chaos/_MOC]]
