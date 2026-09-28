---
type: module
path: "@root/src/PuduLangResilience/Timeout.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Timeout]
---

# PuduLangResilience.Timeout

## Purpose

Hands the callback a token that fires when its time is up and answers `TimedOut` when the callback stopped because of it.

## Interface

### Signatures

```pudu
export type Elapsed = { context: Context.Context, timeout: Int }

export type Options = {
  name: Option[Str],
  timeout: Int,
  generator: Option[fn(Context.Context) -> Int],
  onTimeout: Option[fn(Elapsed) -> ()]
}

export fn defaults() -> Options

export fn after(millis: Int) -> Options

export fn validate(options: &Options) -> Array[Str]

export fn strategy[T, E](options: Options) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], `Std.Concurrent.Cancel`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. The timeout is the generator's answer or the fixed one; zero or less runs the callback unguarded.
2. The callback receives a child token expiring after the timeout (or at the caller's earlier deadline).
3. A `Cancelled` outcome is a timeout only when the child's reason is its own deadline and the caller's token has not fired; then report `OnTimeout` and call `onTimeout`.

## Negative Logic (Prohibited Paths)

- A caller's cancellation or earlier deadline is never reported as a timeout.
- A value returned after the deadline is never replaced.

## Edge Cases

- Options are refused outside 10 ms to one day.

## Depth

DEPTH 0.7 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why not abandon a callback that ignores its token?
  **A:** The callback would keep running on a thread nobody joins. _Rejected:_ a pessimistic mode on a detached thread.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Strategies]]
