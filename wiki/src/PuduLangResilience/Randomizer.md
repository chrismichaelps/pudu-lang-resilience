---
type: module
path: "@root/src/PuduLangResilience/Randomizer.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, seam]
aliases: [PuduLangResilience.Randomizer]
---

# PuduLangResilience.Randomizer

## Purpose

Uniform draws for jitter, injection rates, and weighted choices, replaceable by a seeded or fixed source in tests.

## Interface

### Signatures

```pudu
export type Randomizer = { draw: fn(Int) -> Int }

export const SCALE: Int = 1000000000

export fn system() -> Randomizer

export fn seeded(seed: Int) -> Randomizer

export fn fixed(ratio: Decimal) -> Randomizer

export fn below(source: &Randomizer, bound: Int) -> Int

export fn fraction(source: &Randomizer) -> Int

export fn unit(source: &Randomizer) -> Float

export fn chance(source: &Randomizer, rate: Decimal) -> Bool
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Utils/Numeric]], [[src/PuduLangResilience/Utils/Shared]], `Std.Decimal`, `Std.Random`.
- **Consumed by:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Chaos/Weighted]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Strategy]].

## Algorithm

1. A randomizer answers a whole number below a bound; `system` and `seeded` keep a `Std.Random` generator under a lock.
2. `fixed(r)` always answers `r` of the bound, rounded down.
3. `chance(rate)` compares a draw in parts of `SCALE` (a billion) with the rate's parts; a rate of 0 never holds and 1 always does.

## Negative Logic (Prohibited Paths)

- No draw is taken outside the bound, even from a custom `draw` function.

## Edge Cases

- A bound below one answers zero; a fixed ratio is clamped to just below 1.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why integer parts instead of floats?
  **A:** Rates are `Decimal` in the public surface and compare exactly against integer parts. _Rejected:_ float comparison (inexact at the rate boundaries tests pin).

## Referenced by

[[seams/Runtime]] · [[src/PuduLangResilience/Chaos]] · [[src/PuduLangResilience/Chaos/Weighted]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Utils/Numeric]] · [[src/PuduLangResilience/Utils/Shared]] · [[src/PuduLangResilience/_MOC]]
