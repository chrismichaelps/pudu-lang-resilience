---
type: moc
tags: [moc]
---

# PuduLangResilience.Domain

- [[src/PuduLangResilience/Domain/Algorithms]] — How each limiter kind counts: concurrency, token bucket, fixed window, and sliding window.
- [[src/PuduLangResilience/Domain/Backoff]] — The pure delay before each retry: constant, linear, or exponential growth, a ceiling, and jitter.
- [[src/PuduLangResilience/Domain/Circuit]] — The pure circuit state machine: admission, success, failure, opening, isolation, and closing.
- [[src/PuduLangResilience/Domain/Health]] — Rolling success and failure counts over a sampling period, split into ten windows (one when shorter than 200 ms).
- [[src/PuduLangResilience/Domain/Permits]] — Permit accounting and the wait queue shared by every limiter kind.

## Referenced by

[[src/PuduLangResilience/_MOC]]
