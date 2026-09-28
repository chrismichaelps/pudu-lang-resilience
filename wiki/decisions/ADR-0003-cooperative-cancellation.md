---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0003 — Cancellation is cooperative and no thread is abandoned

## Context

A running thread cannot be interrupted. A timeout or a losing hedged attempt can only ask the
callback to stop.

## Decision

Strategies hand the callback a child token (`Context.withToken`, `Context.fork`). A timeout answers
`TimedOut` only when the callback stopped because of the timeout's own token. Hedging cancels losing
attempts and joins every thread before it answers.

## Consequences

- No thread outlives the call that started it.
- A callback that ignores its token delays the answer; this is documented, not hidden.

## Rejected

- Walking away from a slow callback on a detached thread: leaks the thread and whatever it holds.

## Referenced by

[[CHANGELOG]] · [[decisions/_MOC]] · [[handoffs/2026-09-28-initial-package]] · [[seams/Runtime]]
