---
type: handoff
from_role: Forensic Guardian
to_role: Architect
status: awaiting-release
tags: [handoff, delivery]
---

# Initial package

## Done

- Issue #1 is the ready issue; `feature/1-initial-resilience-package` is branched from `dev`, which
  is branched from the `main` baseline.
- Every module under `src/` has its mirrored page with a resolved Grill Log ([[src/_MOC]]).
- Against the published 0.1.2 compiler: `pudu check`, `pudu fmt --check`, and `pudu lint` are clean
  over `src`, `test`, `tools`, and `examples`; 23 suites pass with 395 assertions; every example
  answers 0.
- On the pull request into `dev`, the Linux `checks` job passes and the `mutation` job kills 75 of
  75 mutants of `Domain/`.
- The vault matches the code, and `test/Package/VaultTest` keeps it so ([[architecture/TESTING]]).

## Decided (do not re-litigate)

- Outcomes, not exceptions ([[decisions/ADR-0001-outcomes-not-exceptions]]); the injected runtime
  ([[decisions/ADR-0002-injected-runtime]]); cooperative cancellation with no abandoned thread
  ([[decisions/ADR-0003-cooperative-cancellation]]).
- `main` receives the package only once the pull request into `dev` is merged with green `checks`
  and `mutation` jobs.

## Open / Remaining

- `dev` is promoted to `main` through a release pull request with green `checks` and `mutation`.
- Tagging `v0.1.0` needs the owner's GitHub identity for `pudu release`.

## Exact next action

Owner: on `main`, run `pudu login`, then `pudu release 0.1.0`.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]

## Referenced by

[[handoffs/_MOC]]
