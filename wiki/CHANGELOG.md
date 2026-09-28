---
type: changelog
tags: [changelog]
---

# Changelog

## 2026-09-28 — Initial package (#1)

- The package `@chrismichaelps/pudu-lang-resilience` 0.1.0 with the module root
  `PuduLangResilience` and the language range `>=0.1.2 <0.2.0`.
- Core vocabulary: `Failure`, `Outcome`, [[src/PuduLangResilience/Context]] with typed properties,
  [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience/Strategy]], and
  [[src/PuduLangResilience/Pipeline]] with validation, nesting, and description
  ([[decisions/ADR-0001-outcomes-not-exceptions]]).
- Strategies: [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/CircuitBreaker]],
  [[src/PuduLangResilience/Timeout]], [[src/PuduLangResilience/Fallback]],
  [[src/PuduLangResilience/Hedging]], and [[src/PuduLangResilience/RateLimiter]]; limiters for
  concurrency, token buckets, fixed and sliding windows, partitions, and chains; chaos injection of
  faults, outcomes, latency, and behavior.
- [[src/PuduLangResilience/Registry]] of named pipelines with reload; telemetry events, severity
  overrides, a meter, and a log listener.
- Time, chance, and telemetry reach strategies only through the [[seams/Runtime]]
  ([[decisions/ADR-0002-injected-runtime]]); cancellation is cooperative and hedging joins every
  attempt ([[decisions/ADR-0003-cooperative-cancellation]]).
- Every sentence the package reports lives in [[src/PuduLangResilience/Constants/Messages]] and is
  filled in one pass by [[src/PuduLangResilience/Utils/Template]].
- Suites for every module, a package layout test, an integration scenario, runnable examples, and
  mutation testing of the pure layer ([[architecture/TESTING]]).

## Referenced by

[[00-INDEX]]
