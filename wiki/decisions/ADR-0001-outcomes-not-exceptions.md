---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0001 — Every result is an outcome value

## Context

Pudu reports failures as values. A resilience layer must add its own rejections (timeouts, open
circuits, rate limits) to whatever the callback can fail with.

## Decision

Callbacks and strategies answer `Outcome[T, E] = Result[T, Failure[E]]`. `Failure` wraps the
callback's error as `Raised(E)` beside the package's own variants. Callbacks that answer a plain
`Result` run through `Pipeline.run`, which lifts their errors.

## Consequences

- Every rejection is matched exhaustively by the caller.
- Predicates decide on values and failures alike.

## Rejected

- A trait object of errors: loses exhaustive matching and needs casts.

## Referenced by

[[decisions/_MOC]]
