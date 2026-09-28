---
type: module
path: "@root/src/PuduLangResilience/Chaos/Fault.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Chaos.Fault]
---

# PuduLangResilience.Chaos.Fault

## Purpose

Answers a failure instead of running the callback, for a share of executions.

## Interface

### Signatures

```pudu
export type Injected[E] = { context: Context.Context, failure: Resilience.Failure[E] }

export type Options[E] = {
  name: Option[Str],
  injection: Chaos.Injection,
  fault: Option[Resilience.Failure[E]],
  generator: Option[fn(Chaos.Arguments) -> Option[Resilience.Failure[E]]],
  onInjected: Option[fn(Injected[E]) -> ()]
}

export fn defaults[E]() -> Options[E]

export fn injecting[E](failure: Resilience.Failure[E], injection: Chaos.Injection) -> Options[E]

export fn validate[E](options: &Options[E]) -> Array[Str]

export fn strategy[T, E](options: Options[E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. When injecting, take the generator's failure or the fixed one; report `Chaos.OnFault`, call `onInjected`, and answer it.

## Negative Logic (Prohibited Paths)

- An injected fault never runs the callback.

## Edge Cases

- A generator answering nothing injects nothing.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why a `Failure` rather than only `E`?
  **A:** Tests often need a timeout or open circuit injected as well. _Rejected:_ callback errors only.

## Referenced by

[[src/PuduLangResilience]] · [[src/PuduLangResilience/Chaos]] · [[src/PuduLangResilience/Chaos/_MOC]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]]
