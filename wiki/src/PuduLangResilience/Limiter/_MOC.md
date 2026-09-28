---
type: moc
tags: [moc]
---

# PuduLangResilience.Limiter

- [[src/PuduLangResilience/Limiter/Concurrency]] — A limiter holding at most `permitLimit` executions at once, with a queue.
- [[src/PuduLangResilience/Limiter/Engine]] — Turns a permit algorithm into a thread-safe limiter with a wait queue.
- [[src/PuduLangResilience/Limiter/FixedWindow]] — A limiter granting at most `permitLimit` permits per window.
- [[src/PuduLangResilience/Limiter/Partitioned]] — One limiter per partition of executions, made on first use from the partition the context names.
- [[src/PuduLangResilience/Limiter/SlidingWindow]] — A limiter granting at most `permitLimit` permits in any window, counted in segments.
- [[src/PuduLangResilience/Limiter/TokenBucket]] — A limiter drawing permits from a bucket refilled by `tokensPerPeriod` every `replenishmentPeriod`, by itself or by hand.

## Referenced by

[[src/PuduLangResilience/_MOC]]
