---
type: module
path: "@root/src/PuduLangResilience/Domain/Circuit.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, deep]
aliases: [PuduLangResilience.Domain.Circuit]
---

# PuduLangResilience.Domain.Circuit

## Purpose

The pure circuit state machine: admission, success, failure, opening, isolation, and closing.

## Interface

### Signatures

```pudu
export type Phase = Closed | Open | HalfOpen | Isolated

export type Machine = {
  phase: Phase,
  blockedUntil: Int,
  halfOpenAttempts: Int,
  health: Health.Health,
  lastHandled: Option[Str]
}

export type Admission = Admitted | Probe | Blocked(Int) | Refused

export type Thresholds = { failureRatio: Decimal, minimumThroughput: Int }

export fn closed(sampling: Int) -> Machine

export fn admit(machine: &Machine, now: Int, breakDuration: Int) -> (Machine, Admission)

export fn succeeded(machine: &Machine, now: Int) -> (Machine, Bool)

export fn failed(machine: &Machine, now: Int, summary: Str, thresholds: &Thresholds) -> (Machine, Bool)

export fn open(machine: &Machine, now: Int, duration: Int) -> Machine

export fn isolate(machine: &Machine) -> Machine

export fn close(machine: &Machine) -> Machine

export fn retryAfter(machine: &Machine, now: Int) -> Int
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Domain/Health]], [[src/PuduLangResilience/Utils/Numeric]], `Std.Decimal`, `Std.Math`.
- **Consumed by:** [[src/PuduLangResilience/CircuitBreaker]].

## Algorithm

1. Closed admits. Isolated refuses. Open past its deadline turns half-open, counts an attempt, pushes the deadline one break further, and admits this one probe; otherwise it blocks with the time left.
2. A success is counted in every phase and closes a half-open circuit.
3. A failure notes the outcome; from half-open it reopens; from closed it is counted and opens at the thresholds; otherwise it is only counted.
4. Closing clears counts, attempts, and the last outcome.

## Negative Logic (Prohibited Paths)

- A half-open circuit admits exactly one probe.

## Edge Cases

- A failure that arrives while open neither extends the break nor reopens it.

## Depth

DEPTH 0.85 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why is a half-open failure not counted?
  **A:** The probe's result decides the circuit directly; counting it would skew the next closed period. _Rejected:_ counting every outcome.

## Referenced by

[[domain/Circuit]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Domain/Health]] · [[src/PuduLangResilience/Domain/_MOC]] · [[src/PuduLangResilience/Utils/Numeric]]
