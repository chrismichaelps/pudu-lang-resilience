---
type: module
path: "@root/src/PuduLangResilience/CircuitBreaker.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.CircuitBreaker]
---

# PuduLangResilience.CircuitBreaker

## Purpose

Stops calling a failing dependency for a break, probes it once, and closes on success; with manual isolation and a state provider.

## Interface

### Signatures

```pudu
export type CircuitState = Closed | Open | HalfOpen | Isolated

export type BreakArguments = { failureRate: Decimal, failureCount: Int, halfOpenAttempts: Int, context: Context.Context }

export type Opening[T, E] = { context: Context.Context, outcome: Option[Resilience.Outcome[T, E]], breakDuration: Int, manual: Bool }

export type Closing[T, E] = { context: Context.Context, outcome: Option[Resilience.Outcome[T, E]], manual: Bool }

export type ManualControl = { isolated: Shared.Shared[Bool], hooks: Shared.Shared[Array[fn(Bool) -> ()]] }

export type StateProvider = { reader: Shared.Shared[Option[fn() -> Circuit.Machine]] }

export type Options[T, E] = {
  name: Option[Str],
  shouldHandle: Predicate.Predicate[T, E],
  failureRatio: Decimal,
  minimumThroughput: Int,
  samplingDuration: Int,
  breakDuration: Int,
  breakDurationGenerator: Option[fn(BreakArguments) -> Int],
  manualControl: Option[ManualControl],
  stateProvider: Option[StateProvider],
  onOpened: Option[fn(Opening[T, E]) -> ()],
  onClosed: Option[fn(Closing[T, E]) -> ()],
  onHalfOpened: Option[fn(Context.Context) -> ()]
}

export fn defaults[T, E]() -> Options[T, E]

export fn validate[T, E](options: &Options[T, E]) -> Array[Str]

export fn manualControl() -> ManualControl

export fn isolate(control: &ManualControl) -> ()

export fn close(control: &ManualControl) -> ()

export fn isIsolated(control: &ManualControl) -> Bool

export fn stateProvider() -> StateProvider

export fn state(provider: &StateProvider) -> Option[CircuitState]

export fn lastHandled(provider: &StateProvider) -> Option[Str]

export fn strategy[T, E](options: Options[T, E]) -> Strategy.Strategy[T, E]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Domain/Circuit]], [[src/PuduLangResilience/Domain/Health]], [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], [[src/PuduLangResilience/Utils/Numeric]], [[src/PuduLangResilience/Utils/Shared]], `Std.Decimal`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Admission and every transition are computed by [[src/PuduLangResilience/Domain/Circuit]] under one lock.
2. Refused: `IsolatedCircuit`. Blocked: `BrokenCircuit(retryAfter)`. Probe: report `OnCircuitHalfOpened` and call `onHalfOpened`.
3. An unhandled outcome records a success and closes a half-open circuit. A handled one records a failure and, when the machine says to open, chooses the break from `breakDurationGenerator` (with failure rate, count, and half-open attempts) or `breakDuration`.
4. A manual control holds hooks for every attached circuit; isolating or closing runs them all and reports through the runtime from `attach`.

## Negative Logic (Prohibited Paths)

- An open or isolated circuit never runs the callback, except the single half-open probe.
- A state provider serves one circuit; attaching it twice is a validation problem.

## Edge Cases

- A cancellation is unhandled by default and counts as a success.
- A circuit attached to an already isolated control starts isolated.
- Closing an already closed circuit reports nothing.

## Depth

DEPTH 0.8 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why is the generator called under the lock?
  **A:** The break must be chosen from the same health the transition read. _Rejected:_ calling it outside (a race could open with stale counts).
- **Q:** Why may pipelines share one strategy value?
  **A:** The circuit belongs to the dependency, not the call site; sharing the value shares the circuit. _Rejected:_ a separate circuit object argument.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Domain/Circuit]] · [[src/PuduLangResilience/Domain/Health]] · [[src/PuduLangResilience/Predicate]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/Utils/Numeric]] · [[src/PuduLangResilience/Utils/Shared]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Strategies]]
