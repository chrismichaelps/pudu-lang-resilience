---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0002 — Strategies reach time, chance, and telemetry through the pipeline

## Context

Retry delays, circuit breaks, limiter windows, jitter, and injection rates all depend on time or
randomness. Tests that wait real time are slow and flaky.

## Decision

`Pipeline.Options` holds a clock, a randomizer, and listeners. The pipeline hands each strategy a
[[seams/Runtime]] on `attach` and on every execution; strategies use nothing else.

## Consequences

- Retry, circuit, limiter, and chaos suites run on a manual clock and fixed draws, instantly.
- Package users get the same determinism in their own tests.

## Rejected

- Module-level clocks: global state shared by unrelated pipelines.

## Referenced by

[[CHANGELOG]] · [[decisions/_MOC]] · [[handoffs/2026-09-28-initial-package]]
