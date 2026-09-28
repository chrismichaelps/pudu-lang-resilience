---
type: module
path: "@root/src/PuduLangResilience/Domain/Backoff.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, deep]
aliases: [PuduLangResilience.Domain.Backoff]
---

# PuduLangResilience.Domain.Backoff

## Purpose

The pure delay before each retry: constant, linear, or exponential growth, a ceiling, and jitter.

## Interface

### Signatures

```pudu
export type Shape = Constant | Linear | Exponential

export type Plan = { shape: Shape, delay: Int, maxDelay: Option[Int], useJitter: Bool }

export fn next(plan: &Plan, attempt: Int, state: Float, draw: Float) -> (Int, Float)

export fn steady(plan: &Plan, attempt: Int) -> Int
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Utils/Numeric]], `Std.Math`, `Std.Math.Float`, `Std.Option`.
- **Consumed by:** [[src/PuduLangResilience/Retry]].

## Algorithm

1. A zero base answers zero. Constant: the base. Linear: `(attempt + 1) * base`. Exponential: the base doubled `attempt` times. All saturate.
2. Constant and linear jitter move the delay by up to a quarter either way: `d + d/2 * draw - d/4`.
3. Exponential jitter follows the decorrelated curve `2^t * tanh(sqrt(4t))` at `t = attempt + draw`, less the previous value, times the base divided by 1.4; a curve that stops being finite saturates.
4. The ceiling caps the result.

## Negative Logic (Prohibited Paths)

- No delay is negative or overflows.

## Edge Cases

- At draw 0.5 constant jitter keeps the delay exactly.

## Depth

DEPTH 0.85 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why the decorrelated curve for exponential jitter?
  **A:** Its medians stay near whole multiples of the base while spreading clients that failed together. _Rejected:_ full jitter (median halves the intended wait).

## Referenced by

[[src/PuduLangResilience/Domain/_MOC]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Utils/Numeric]]
