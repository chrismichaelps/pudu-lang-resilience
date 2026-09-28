---
type: module
path: "@root/src/PuduLangResilience/Telemetry.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.65
depth_status: MEDIUM
tags: [module, seam]
aliases: [PuduLangResilience.Telemetry]
---

# PuduLangResilience.Telemetry

## Purpose

Events strategies report while executing, the listeners that receive them, and the severity override a pipeline applies.

## Interface

### Signatures

```pudu
export type Severity = Silent | Debug | Information | Warning | Error | Critical

export type Source = { pipelineName: Option[Str], pipelineInstance: Option[Str], strategyName: Option[Str] }

export type Detail
  = Executing
  | Executed(Int)
  | Attempted(Int, Int, Bool)
  | Retrying(Int, Int)
  | Opened(Int, Bool)
  | Closed(Bool)
  | HalfOpened
  | FellBack
  | Hedged(Int)
  | TimeoutElapsed(Int)
  | Rejected(Option[Int])
  | FaultInjected
  | OutcomeInjected
  | LatencyInjected(Int)
  | BehaviorInjected

export type Event = {
  name: Str,
  severity: Severity,
  source: Source,
  operationKey: Option[Str],
  outcome: Option[Str],
  detail: Detail
}

export type Listener = fn(Event) -> ()

export type Reporter = { source: Source, emit: fn(Event) -> () }

export fn reporter(source: Source, listeners: &Array[Listener], severityOf: &Option[fn(Event) -> Severity]) -> Reporter

export fn silent() -> Reporter

export fn forStrategy(base: &Reporter, strategyName: Option[Str]) -> Reporter

export fn report(target: &Reporter, context: &Context.Context, name: Str, severity: Severity, outcome: Option[Str], detail: Detail) -> ()

export fn severityName(value: &Severity) -> Str

export fn rank(value: &Severity) -> Int

export fn sourceName(value: &Source) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Context]], `Std.Option`.
- **Consumed by:** [[src/PuduLangResilience/Chaos/Behavior]], [[src/PuduLangResilience/Chaos/Fault]], [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/Chaos/Outcome]], [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/RateLimiter]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry/Log]], [[src/PuduLangResilience/Telemetry/Meter]], [[src/PuduLangResilience/Timeout]].

## Algorithm

1. A `Reporter` stamps its source (pipeline name, instance, strategy name) on each event.
2. The pipeline's reporter applies `severityOf` when given and drops `Silent` events before any listener sees them.
3. `Detail` carries each event kind's measurements as a typed variant.

## Negative Logic (Prohibited Paths)

- No listener receives a `Silent` event.

## Edge Cases

- A source with no names reads as `(unnamed)`.

## Depth

DEPTH 0.65 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why typed details rather than a property bag?
  **A:** Listeners match exhaustively and cannot misread a duration as a delay. _Rejected:_ string-keyed attributes.

## Referenced by

[[seams/Runtime]] · [[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry/Log]] · [[src/PuduLangResilience/Telemetry/Meter]] · [[src/PuduLangResilience/Timeout]] · [[src/PuduLangResilience/_MOC]]
