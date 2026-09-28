---
type: module
path: "@root/src/PuduLangResilience/Chaos/Weighted.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Chaos.Weighted]
---

# PuduLangResilience.Chaos.Weighted

## Purpose

A generator picking one of several values with chances in proportion to their weights.

## Interface

### Signatures

```pudu
export fn choose[V](choices: &Array[(Int, V)]) -> fn(Chaos.Arguments) -> Option[V]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Randomizer]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Draw below the total of positive weights and walk the choices until the draw falls inside one.

## Negative Logic (Prohibited Paths)

- A weight below one is never chosen.

## Edge Cases

- With no positive weight nothing is chosen.

## Depth

DEPTH 0.5 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why integer weights?
  **A:** They sum exactly and draw exactly. _Rejected:_ decimal probabilities.

## Referenced by

[[src/PuduLangResilience/Chaos/_MOC]]
