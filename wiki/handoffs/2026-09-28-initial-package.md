---
type: handoff
from_role: Forensic Guardian
to_role: Architect
status: complete
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
- PR #2 into `dev` and PR #4 into `main` both passed `checks` and `mutation` on Linux; the push
  run on `main` passed as well.
- Released 0.1.0 as tag `v0.1.0` with a GitHub release; `pudu search` lists
  `@chrismichaelps/pudu-lang-resilience` with 0.1.0 as latest.

## Decided (do not re-litigate)

- Outcomes, not exceptions ([[decisions/ADR-0001-outcomes-not-exceptions]]); the injected runtime
  ([[decisions/ADR-0002-injected-runtime]]); cooperative cancellation with no abandoned thread
  ([[decisions/ADR-0003-cooperative-cancellation]]).
- `main` receives the package only once the pull request into `dev` is merged with green `checks`
  and `mutation` jobs.

## Open / Remaining

- None for the initial package.

## Exact next action

None; the initial package is released.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]

## Referenced by

[[CHANGELOG]] · [[handoffs/_MOC]]
