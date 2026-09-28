---
type: module
path: "@root/src/PuduLangResilience/Context.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.75
depth_status: DEEP
tags: [module, backbone]
aliases: [PuduLangResilience.Context]
---

# PuduLangResilience.Context

## Purpose

What one execution carries through a pipeline: an operation key, the cancellation token strategies and the callback observe, and typed properties shared between them.

## Interface

### Signatures

```pudu
export type Context = {
  operationKey: Option[Str],
  token: Cancel.Token,
  properties: Shared.Shared[Map[Str, Str]]
}

export type Key[T] = { name: Str, encode: fn(T) -> Str, decode: fn(Str) -> Option[T] }

export fn create() -> Context

export fn keyed(operationKey: Str) -> Context

export fn cancellable(token: Cancel.Token) -> Context

export fn withToken(source: &Context, token: Cancel.Token) -> Context

export fn fork(source: &Context, token: Cancel.Token) -> Context

export fn adopt(target: &Context, source: &Context) -> ()

export fn key[T](name: Str, encode: fn(T) -> Str, decode: fn(Str) -> Option[T]) -> Key[T]

export fn textKey(name: Str) -> Key[Str]

export fn intKey(name: Str) -> Key[Int]

export fn boolKey(name: Str) -> Key[Bool]

export fn set[T](target: &Context, name: &Key[T], value: T) -> ()

export fn get[T](source: &Context, name: &Key[T]) -> Option[T]

export fn getOr[T](source: &Context, name: &Key[T], fallback: T) -> T

export fn has[T](source: &Context, name: &Key[T]) -> Bool

export fn remove[T](target: &Context, name: &Key[T]) -> ()

export fn names(source: &Context) -> Array[Str]

export fn cancel(target: &Context, why: Str) -> ()

export fn stopped(source: &Context) -> Bool

export fn check[E](source: &Context) -> Result[(), Resilience.Failure[E]]

export fn pause[E](source: &Context, millis: Int) -> Result[(), Resilience.Failure[E]]
```

### Linkage

- **Requires:** [[src/PuduLangResilience]], [[src/PuduLangResilience/Utils/Shared]], `Std.Concurrent.Cancel`, `Std.Decimal`, `Std.Map`, `Std.Option`.
- **Consumed by:** [[src/PuduLangResilience/Chaos]], [[src/PuduLangResilience/Chaos/Behavior]], [[src/PuduLangResilience/Chaos/Fault]], [[src/PuduLangResilience/Chaos/Latency]], [[src/PuduLangResilience/Chaos/Outcome]], [[src/PuduLangResilience/CircuitBreaker]], [[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]], [[src/PuduLangResilience/Hedging/Attempts]], [[src/PuduLangResilience/Limiter/Partitioned]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Predicate]], [[src/PuduLangResilience/RateLimiter]], [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], [[src/PuduLangResilience/Timeout]].

## Algorithm

1. Properties are text under a name in shared state; a `Key[T]` encodes and decodes its values.
2. `withToken` keeps the same properties; `fork` copies them for a hedged attempt, and `adopt` copies an attempt's back.
3. `check` and `pause` turn the token's reason into `Cancelled` so a callback stops with `?`.

## Negative Logic (Prohibited Paths)

- A strategy never replaces the caller's token; it hands the callback a child token.

## Edge Cases

- A value that does not decode reads as absent while still counting as present for `has`.
- The first cancellation reason is kept.

## Depth

DEPTH 0.75 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why text-encoded properties?
  **A:** A heterogeneous typed map cannot be expressed without runtime types; a key's codec keeps the typed surface while storing one map. _Rejected:_ one property type per context (too narrow); JSON values (a dependency for no gain).
- **Q:** Why no context pool?
  **A:** Contexts are small records; pooling exists to avoid allocation churn the runtime does not have. _Rejected:_ a pool (lifetime bugs for no measured benefit).

## Referenced by

[[CHANGELOG]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Chaos]] · [[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Hedging/Attempts]] · [[src/PuduLangResilience/Limiter/Partitioned]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Predicate]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/Timeout]] · [[src/PuduLangResilience/Utils/Shared]] · [[src/PuduLangResilience/_MOC]]
