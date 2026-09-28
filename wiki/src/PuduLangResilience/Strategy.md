---
type: module
path: "@root/src/PuduLangResilience/Strategy.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, seam, backbone]
aliases: [PuduLangResilience.Strategy]
---

# PuduLangResilience.Strategy

## Purpose

The contract of one pipeline layer: its name, kind, summary, validation problems, the `attach` hook, and the `execute` function that runs a callback under its policy.

## Interface

### Signatures

```pudu
export type Callback[T, E] = fn(Context.Context) -> Resilience.Outcome[T, E]

export type Runtime = { clock: Clock.Clock, randomizer: Randomizer.Randomizer, telemetry: Telemetry.Reporter }

export type Execute[T, E] = fn(Runtime, Context.Context, Callback[T, E]) -> Resilience.Outcome[T, E]

export type Strategy[T, E] = {
  name: Option[Str],
  kind: Str,
  summary: Str,
  problems: Array[Str],
  attach: fn(Runtime) -> (),
  execute: Execute[T, E]
}

export fn custom[T, E](name: Option[Str], kind: Str, execute: Execute[T, E]) -> Strategy[T, E]

export fn detached(_runtime: Runtime) -> ()

export fn standalone() -> Runtime
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Randomizer]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Telemetry]].
- **Consumed by:** [[src/PuduLangResilience/Chaos/Behavior]], [[src/PuduLangResilience/Chaos/Fault]], [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/Chaos/Outcome]], [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/RateLimiter]], [[src/PuduLangResilience/Registry]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Timeout]].

## Algorithm

1. A pipeline calls `attach` once per strategy with the strategy's [[seams/Runtime]] when it is built.
2. `custom` builds a stateless strategy from any execute function; `detached` is the attach of a strategy that keeps nothing.

## Negative Logic (Prohibited Paths)

- A strategy performs effects only through the runtime it is given.

## Edge Cases

- A strategy held by two pipelines is attached twice; the last attach wins for manual transitions.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why `attach`?
  **A:** A circuit breaker's manual control reports transitions outside any execution and needs the pipeline's telemetry and clock. _Rejected:_ capturing the runtime on first execution (manual isolation before any call would report nowhere).

## Referenced by

[[src/PuduLangResilience/_MOC]] · [[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Registry]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Timeout]]
