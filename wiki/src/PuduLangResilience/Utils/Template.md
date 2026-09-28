---
type: module
path: "@root/src/PuduLangResilience/Utils/Template.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, leaf]
aliases: [PuduLangResilience.Utils.Template]
---

# PuduLangResilience.Utils.Template

## Purpose

Fills the numbered slots of a message template.

## Interface

### Signatures

```pudu
export fn fill(template: Str, values: &Array[Str]) -> Str
```

### Linkage

- **Requires:** `Std.List`, `Std.Text`.
- **Consumed by:** [[src/PuduLangResilience]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Registry]].

## Algorithm

1. Scan left to right for `<`. When the text up to the next `>` is a slot number with a value, emit the value and continue after the slot; otherwise emit the `<` as text.
2. Text taken from a value is never scanned again.

## Negative Logic (Prohibited Paths)

- A value containing slot text (a registry key such as `<2>`) is never filled a second time.

## Edge Cases

- A slot without a value, slot `<0>`, and stray angle brackets are kept as written.

## Depth

DEPTH 0.6 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why not repeated `replace`?
  **A:** A value holding `<2>` would be filled by the next replacement, corrupting messages built from caller text. _Rejected:_ replace per slot.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Constants/Messages]] · [[src/PuduLangResilience/Constants/_MOC]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Registry]] · [[src/PuduLangResilience/Utils/_MOC]]
