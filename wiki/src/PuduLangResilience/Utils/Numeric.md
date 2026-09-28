---
type: module
path: "@root/src/PuduLangResilience/Utils/Numeric.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, leaf]
aliases: [PuduLangResilience.Utils.Numeric]
---

# PuduLangResilience.Utils.Numeric

## Purpose

Saturating integer arithmetic for durations and counts, and the conversions between `Int`, `Float`, and `Decimal` the compiler does not provide.

## Interface

### Signatures

```pudu
export const LARGEST: Int = 9223372036854775807

export fn add(left: Int, right: Int) -> Int

export fn multiply(left: Int, right: Int) -> Int

export fn toFloat(value: Int) -> Float

export fn toInt(value: Float) -> Int

export fn partsOf(ratio: Decimal, whole: Int) -> Int
```

### Linkage

- **Requires:** `Std.Decimal`, `Std.Math.Float`, `Std.Option`.
- **Consumed by:** [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Domain/Algorithms]], [[src/PuduLangResilience/Domain/Backoff]], [[src/PuduLangResilience/Domain/Circuit]], [[src/PuduLangResilience/Domain/Health]], [[src/PuduLangResilience/Domain/Permits]], [[src/PuduLangResilience/Randomizer]].

## Algorithm

1. `add` and `multiply` answer `LARGEST` instead of overflowing.
2. `toFloat` goes through `Decimal.toFloat64`; `toInt` floors the float, parses its text back as a decimal, and clamps to 0 below and `LARGEST` above or when not finite.
3. `partsOf` floors `ratio * whole` exactly in decimal.

## Negative Logic (Prohibited Paths)

- No arithmetic on a duration or count can overflow.

## Edge Cases

- Floats at or above 9e18 saturate before parsing; exponent notation parses.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why parse text to convert a float?
  **A:** Pudu 0.1.2 has no built-in float-to-int conversion; the decimal parser accepts every rendering `show` produces, including exponents. _Rejected:_ a bit-by-bit binary conversion (longer, same result).

## Referenced by

[[grammar/pudu]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Domain/Algorithms]] · [[src/PuduLangResilience/Domain/Backoff]] · [[src/PuduLangResilience/Domain/Circuit]] · [[src/PuduLangResilience/Domain/Health]] · [[src/PuduLangResilience/Domain/Permits]] · [[src/PuduLangResilience/Randomizer]] · [[src/PuduLangResilience/Utils/_MOC]]
