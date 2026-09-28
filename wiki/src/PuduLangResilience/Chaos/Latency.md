---
type: module
path: "@root/src/PuduLangResilience/Chaos/Latency.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Chaos.Latency]
---

# PuduLangResilience.Chaos.Latency

## Purpose

Delays a callback on the pipeline clock before running it, for a share of executions.

## Interface

### Signatures

```pudu
export type Injected = { context: Context.Context, latency: Int }

export type Options = {
  name: Option[Str],
  injection: Chaos.Injection,
  latency: Int,
  generator: Option[fn(Chaos.Arguments) -> Int],
  onInjected: Option[fn(Injected) -> ()]
}

export fn defaults() -> Options

export fn injecting(millis: Int, injection: Chaos.Injection) -> Options

export fn validate(options: &Options) -> Array[Str]

export fn strategy[T, E](options: Options) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], `Std.Concurrent.Cancel`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. When injecting, report `Chaos.OnLatency`, wait on the clock with the context's token, call `onInjected`, and run the callback.

## Negative Logic (Prohibited Paths)

- A delay of zero or less injects nothing.

## Edge Cases

- A token firing during the delay answers `Cancelled` without running the callback.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why the pipeline clock?
  **A:** A manual clock makes injected latency instant and visible in tests. _Rejected:_ the system clock.

## Referenced by

[[src/PuduLangResilience]] · [[src/PuduLangResilience/Chaos]] · [[src/PuduLangResilience/Chaos/_MOC]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]]
