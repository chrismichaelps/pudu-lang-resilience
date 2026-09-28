---
type: handoff
from_role: Forensic Guardian
to_role: Architect
status: in-progress
tags: [handoff, delivery]
---

# Initial package

## Done

- Issue #1 is the ready issue; `feature/1-initial-resilience-package` is branched from `dev`, which
  is branched from the `main` baseline.
- Every module under `src/` has its mirrored page with a resolved Grill Log ([[src/_MOC]]).
- Against the published 0.1.2 compiler: `pudu check`, `pudu fmt --check`, and `pudu lint` are clean
  over `src`, `test`, `tools`, and `examples`; every suite passes; every example answers 0.

## Decided (do not re-litigate)

- Outcomes, not exceptions ([[decisions/ADR-0001-outcomes-not-exceptions]]); the injected runtime
  ([[decisions/ADR-0002-injected-runtime]]); cooperative cancellation with no abandoned thread
  ([[decisions/ADR-0003-cooperative-cancellation]]).
- `main` receives the package only once the pull request into `dev` is merged with green `checks`
  and `mutation` jobs.

## Open / Remaining

- Merge the pull request into `dev` once CI is green, then promote `dev` to `main` and release
  `0.1.0` with `pudu release`.

## Exact next action

Architect: confirm the `checks` and `mutation` jobs on the pull request into `dev`, then merge it.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]

## Referenced by

[[handoffs/_MOC]]
