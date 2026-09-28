---
type: module
path: "@root/src/PuduLangResilience/Constants/Messages.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
tags: [module, leaf]
aliases: [PuduLangResilience.Constants.Messages]
---

# PuduLangResilience.Constants.Messages

## Purpose

Every sentence the package reports to a caller, as templates with numbered slots filled by [[src/PuduLangResilience/Utils/Template]].

## Interface

### Signatures

```pudu
export const RAISED: Str = "The operation failed: <1>"

export const TIMED_OUT: Str = "The operation did not complete within <1> ms."

export const BROKEN_CIRCUIT: Str = "The circuit is open; it may be tried again in <1> ms."

export const ISOLATED_CIRCUIT: Str = "The circuit is isolated and admits no execution."

export const RATE_LIMITED_AFTER: Str = "The rate limiter rejected the execution; retry after <1> ms."

export const RATE_LIMITED: Str = "The rate limiter rejected the execution."

export const CANCELLED: Str = "The operation was cancelled: <1>"

export const CRASHED: Str = "The operation stopped its thread: <1>"

export const SUCCEEDED: Str = "Ok(<1>)"

export const PIPELINE_INVALID: Str = "The pipeline is invalid: <1>"

export const REGISTRY_NOT_FOUND: Str = "No pipeline builder is registered for '<1>'."

export const REGISTRY_INVALID: Str = "The pipeline for '<1>' is invalid: <2>"

export const PROBLEM_SEPARATOR: Str = "; "
```

### Linkage

- **Requires:** nothing.
- **Consumed by:** [[src/PuduLangResilience]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Registry]].

## Algorithm

1. Each template names its slots `<1>`, `<2>` in the order the caller supplies values.

## Negative Logic (Prohibited Paths)

- No module composes user-facing wording inline; a change of wording is a change to this module alone.

## Edge Cases

- `PROBLEM_SEPARATOR` joins several validation problems into one sentence.

## Depth

DEPTH 0.3 (SHALLOW). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why templates instead of prefix and suffix constants?
  **A:** A sentence with a value in the middle stays readable as one string, and a reordering of the value does not split constants. _Rejected:_ concatenated fragments (hard to read, easy to break).

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Constants/_MOC]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Registry]]
