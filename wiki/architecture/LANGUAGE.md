---
type: language
tags: [architecture]
aliases: [Vocabulary]
---

# Architecture vocabulary

- **Module** — one `.pudu` file and its mirrored page under `wiki/src/`.
- **Interface** — a module's exported signatures; what callers may rely on.
- **Depth** — how much behaviour an interface hides relative to its size. Recorded per page.
- **Seam** — a boundary where one implementation replaces another without editing callers:
  [[seams/Runtime]] and [[seams/Limiter]].
- **Callback** — the caller's work, a function from a context to an outcome.
- **Outcome** — a value or a failure. See [[domain/Outcome]].
- **Strategy** — one layer of policy around a callback. See [[domain/Strategy]].
- **Pipeline** — strategies composed outermost first. See [[domain/Pipeline]].
- **Reactive / Proactive** — a strategy that decides from the outcome (retry, circuit breaker,
  fallback, hedging) or before and during the run (timeout, rate limiter, chaos).
- **Handled** — an outcome a reactive strategy's predicate accepts and acts on.
- **Rejection** — a failure a strategy produces itself: timed out, broken or isolated circuit,
  rate limited.
- **Circuit** — the state a circuit breaker keeps about a dependency. See [[domain/Circuit]].
- **Permit / Lease** — what a limiter grants and how it is handed back. See [[domain/Permit]].
- **Injection** — chaos a strategy adds to a share of executions. See [[domain/Injection]].

## Referenced by

[[architecture/_MOC]]
