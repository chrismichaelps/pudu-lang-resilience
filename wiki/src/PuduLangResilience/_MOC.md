---
type: moc
tags: [moc]
---

# PuduLangResilience

- [[src/PuduLangResilience/Chaos]] — When a chaos strategy injects: the rate, its generator, and whether injection is enabled for an execution.
- [[src/PuduLangResilience/CircuitBreaker]] — Stops calling a failing dependency for a break, probes it once, and closes on success; with manual isolation and a state provider.
- [[src/PuduLangResilience/Clock]] — The monotonic time and cancellable waiting every strategy uses, as a record of functions so tests substitute a manual clock ([[seams/Runtime]]).
- [[src/PuduLangResilience/Context]] — What one execution carries through a pipeline: an operation key, the cancellation token strategies and the callback observe, and typed properties shared between them.
- [[src/PuduLangResilience/Fallback]] — Answers a substitute outcome in place of one its predicate handles.
- [[src/PuduLangResilience/Hedging]] — Races extra attempts against a slow or failing one and answers the first acceptable outcome.
- [[src/PuduLangResilience/Limiter]] — The contract every limiter answers: acquire with waiting, attempt without, statistics, and replenishment by hand; leases and chaining.
- [[src/PuduLangResilience/Pipeline]] — Composes strategies into one executable policy, validates them, and reports each execution's start and end.
- [[src/PuduLangResilience/Predicate]] — The shared shape of every reactive strategy's `shouldHandle`: which outcomes, errors, values, or rejections a strategy acts on.
- [[src/PuduLangResilience/Randomizer]] — Uniform draws for jitter, injection rates, and weighted choices, replaceable by a seeded or fixed source in tests.
- [[src/PuduLangResilience/RateLimiter]] — Admits an execution only when a limiter grants it a permit, and returns the permit when the execution ends.
- [[src/PuduLangResilience/Registry]] — Named pipelines built once from a registered builder, shared by every caller, and rebuilt on `reload`.
- [[src/PuduLangResilience/Retry]] — Runs a callback again after outcomes its predicate handles, waiting a computed delay between attempts.
- [[src/PuduLangResilience/Strategy]] — The contract of one pipeline layer: its name, kind, summary, validation problems, the `attach` hook, and the `execute` function that runs a callback under its policy.
- [[src/PuduLangResilience/Telemetry]] — Events strategies report while executing, the listeners that receive them, and the severity override a pipeline applies.
- [[src/PuduLangResilience/Timeout]] — Hands the callback a token that fires when its time is up and answers `TimedOut` when the callback stopped because of it.
- [[src/PuduLangResilience/Chaos/_MOC]] — the modules under `PuduLangResilience.Chaos`.
- [[src/PuduLangResilience/Constants/_MOC]] — the modules under `PuduLangResilience.Constants`.
- [[src/PuduLangResilience/Domain/_MOC]] — the modules under `PuduLangResilience.Domain`.
- [[src/PuduLangResilience/Hedging/_MOC]] — the modules under `PuduLangResilience.Hedging`.
- [[src/PuduLangResilience/Limiter/_MOC]] — the modules under `PuduLangResilience.Limiter`.
- [[src/PuduLangResilience/Telemetry/_MOC]] — the modules under `PuduLangResilience.Telemetry`.
- [[src/PuduLangResilience/Utils/_MOC]] — the modules under `PuduLangResilience.Utils`.

## Referenced by

[[src/_MOC]]
