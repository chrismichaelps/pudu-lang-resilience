---
type: module
path: "@root/src/PuduLangResilience/Limiter/Partitioned.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.55
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangResilience.Limiter.Partitioned]
---

# PuduLangResilience.Limiter.Partitioned

## Purpose

One limiter per partition of executions, made on first use from the partition the context names.

## Interface

### Signatures

```pudu
export type Partition = { key: Str, create: fn() -> Limiter.Limiter }

export type Partitioned = { partitionOf: fn(Context.Context) -> Partition, limiters: Shared.Shared[Map[Str, Limiter.Limiter]] }

export fn create(partitionOf: fn(Context.Context) -> Partition) -> Partitioned

export fn acquire(target: &Partitioned, context: &Context.Context, permits: Int) -> Limiter.Lease

export fn attempt(target: &Partitioned, context: &Context.Context, permits: Int) -> Limiter.Lease

export fn statistics(source: &Partitioned, key: Str) -> Option[Limiter.Statistics]

export fn keys(source: &Partitioned) -> Array[Str]
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Limiter]], [[src/PuduLangResilience/Utils/Shared]], `Std.Map`.
- **Consumed by:** [[src/PuduLangResilience/RateLimiter]].

## Algorithm

1. The partitioner answers a key and a factory; the limiter for a key is made under the lock the first time.

## Negative Logic (Prohibited Paths)

- A factory runs at most once per key.

## Edge Cases

- An unused partition has no statistics.

## Depth

DEPTH 0.55 (MEDIUM). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why is the factory in the partition?
  **A:** Different partitions may need different limits. _Rejected:_ one factory for all keys.

## Referenced by

[[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Limiter]] · [[src/PuduLangResilience/Limiter/_MOC]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Utils/Shared]]
