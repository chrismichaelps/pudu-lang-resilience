---
type: seam
capacity: CRITICAL
tags: [seam]
---

# Limiter (seam)

## Classification

The record of functions a rate limiter strategy takes permits from:
[[src/PuduLangResilience/Limiter]]. A caller may pass any `acquire` function.

## Adapters

- **Built in** — concurrency, token bucket, fixed window, sliding window (through
  [[src/PuduLangResilience/Limiter/Engine]]), partitioned, and chains of any of them.
- **Custom** — any function from a context to a lease.

## Health

Every lease is released once, whatever the callback answers.

## Referenced by

[[architecture/LANGUAGE]] · [[seams/_MOC]] · [[subsystems/Limiting]]
