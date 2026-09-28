---
type: module
path: "@root/src/PuduLangResilience/Chaos.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Chaos]
---

# PuduLangResilience.Chaos

## Purpose

When a chaos strategy injects: the rate, its generator, and whether injection is enabled for an execution.

## Interface

### Signatures

```pudu
export type Injection = {
  rate: Decimal,
  rateGenerator: Option[fn(Context.Context) -> Decimal],
  enabled: Bool,
  enabledGenerator: Option[fn(Context.Context) -> Bool]
}

export type Arguments = { context: Context.Context, randomizer: Randomizer.Randomizer }

export fn defaults() -> Injection

export fn always() -> Injection

export fn atRate(rate: Decimal) -> Injection

export fn validate(injection: &Injection) -> Array[Str]

export fn shouldInject(injection: &Injection, context: &Context.Context, randomizer: &Randomizer.Randomizer) -> Bool

export fn arguments(context: &Context.Context, randomizer: &Randomizer.Randomizer) -> Arguments
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Randomizer]], `Std.Decimal`.
- **Consumed by:** [[src/PuduLangResilience/Chaos/Behavior]], [[src/PuduLangResilience/Chaos/Fault]], [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/Chaos/Outcome]], [[src/PuduLangResilience/Chaos/Weighted]].

## Algorithm

1. Disabled (fixed or generated) never injects; otherwise draw against the rate, generated or fixed.

## Negative Logic (Prohibited Paths)

- A disabled strategy never draws.

## Edge Cases

- A generated rate outside 0 to 1 acts as the nearer bound.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why clamp a generated rate instead of failing?
  **A:** A generator runs per execution; failing the caller's call for a chaos misconfiguration is worse than clamping. _Rejected:_ answering an error.

## Referenced by

[[domain/Injection]] · [[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/Chaos/Weighted]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Randomizer]] · [[src/PuduLangResilience/_MOC]] · [[subsystems/Chaos]]
