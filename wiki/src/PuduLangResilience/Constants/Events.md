---
type: module
path: "@root/src/PuduLangResilience/Constants/Events.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
tags: [module, leaf]
aliases: [PuduLangResilience.Constants.Events]
---

# PuduLangResilience.Constants.Events

## Purpose

The name of every telemetry event, so listeners and meters match on constants rather than retyped text.

## Interface

### Signatures

```pudu
export const PIPELINE_EXECUTING: Str = "PipelineExecuting"

export const PIPELINE_EXECUTED: Str = "PipelineExecuted"

export const EXECUTION_ATTEMPT: Str = "ExecutionAttempt"

export const ON_RETRY: Str = "OnRetry"

export const ON_CIRCUIT_OPENED: Str = "OnCircuitOpened"

export const ON_CIRCUIT_CLOSED: Str = "OnCircuitClosed"

export const ON_CIRCUIT_HALF_OPENED: Str = "OnCircuitHalfOpened"

export const ON_FALLBACK: Str = "OnFallback"

export const ON_HEDGING: Str = "OnHedging"

export const ON_TIMEOUT: Str = "OnTimeout"

export const ON_RATE_LIMITER_REJECTED: Str = "OnRateLimiterRejected"

export const ON_FAULT: Str = "Chaos.OnFault"

export const ON_OUTCOME: Str = "Chaos.OnOutcome"

export const ON_LATENCY: Str = "Chaos.OnLatency"

export const ON_BEHAVIOR: Str = "Chaos.OnBehavior"
```

### Linkage

- **Requires:** nothing.
- **Consumed by:** [[src/PuduLangResilience/Chaos/Behavior]], [[src/PuduLangResilience/Chaos/Fault]], [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/Chaos/Outcome]], [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/RateLimiter]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Timeout]].

## Algorithm

1. One `const` per event; strategies pass them to `Telemetry.report`.

## Negative Logic (Prohibited Paths)

- No event name is written anywhere else in `src/`.

## Edge Cases

- Chaos events carry the `Chaos.` prefix so a meter can group them.

## Depth

DEPTH 0.3 (SHALLOW). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why constants rather than a sum type of event kinds?
  **A:** Custom strategies report their own event names; a closed sum type would forbid that. _Rejected:_ an enum of built-in events.

## Referenced by

[[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Constants/_MOC]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Timeout]]
