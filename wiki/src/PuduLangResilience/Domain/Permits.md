---
type: module
path: "@root/src/PuduLangResilience/Domain/Permits.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, deep]
aliases: [PuduLangResilience.Domain.Permits]
---

# PuduLangResilience.Domain.Permits

## Purpose

Permit accounting and the wait queue shared by every limiter kind.

## Interface

### Signatures

```pudu
export type Algorithm[S] = {
  refresh: fn(S, Int) -> S,
  available: fn(S) -> Int,
  take: fn(S, Int) -> S,
  giveBack: fn(S, Int) -> S,
  retryAfter: fn(S, Int, Int) -> Option[Int],
  replenish: fn(S) -> (S, Bool)
}

export type Order = Oldest | Newest

export type Waiter = { ticket: Int, permits: Int }

export type Book[S] = {
  held: S,
  waiters: Array[Waiter],
  evicted: Array[Int],
  nextTicket: Int,
  failed: Int,
  succeeded: Int,
  permitLimit: Int,
  queueLimit: Int,
  order: Order
}

export type Decision = Grant | Deny(Option[Int]) | Queue(Int)

export type Turn = Taken | Evicted | Waiting

export fn open[S](held: S, permitLimit: Int, queueLimit: Int, order: Order) -> Book[S]

export fn request[S](book: &Book[S], algorithm: &Algorithm[S], now: Int, permits: Int, mayQueue: Bool) -> (Book[S], Decision)

export fn poll[S](book: &Book[S], algorithm: &Algorithm[S], now: Int, ticket: Int) -> (Book[S], Turn)

export fn withdraw[S](book: &Book[S], ticket: Int) -> Book[S]

export fn giveBack[S](book: &Book[S], algorithm: &Algorithm[S], permits: Int) -> Book[S]

export fn replenish[S](book: &Book[S], algorithm: &Algorithm[S]) -> (Book[S], Bool)

export fn available[S](book: &Book[S], algorithm: &Algorithm[S], now: Int) -> Int

export fn queued[S](book: &Book[S]) -> Int
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Utils/Numeric]], `Std.List`.
- **Consumed by:** [[src/PuduLangResilience/Domain/Algorithms]], [[src/PuduLangResilience/Limiter/Engine]].

## Algorithm

1. A request refreshes the algorithm at `now`. More than the limit or negative: denied. Zero: granted while any permit is free.
2. Granted when the permits are free and no older waiter comes first; otherwise queued if allowed and there is room, evicting the oldest waiters when newest are served first; otherwise denied with the algorithm's retry time.
3. A waiter takes its permits on its turn: the oldest or newest waiter, whichever the order serves, when enough are free.

## Negative Logic (Prohibited Paths)

- Queued permits never exceed the queue limit.

## Edge Cases

- An evicted or withdrawn waiter counts as a failure.
- Returned permits never exceed the concurrency limit.

## Depth

DEPTH 0.85 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why is the queue counted in permits?
  **A:** A request for many permits takes proportionally more room. _Rejected:_ counting requests.

## Referenced by

[[src/PuduLangResilience/Domain/_MOC]] · [[src/PuduLangResilience/Domain/Algorithms]] · [[src/PuduLangResilience/Limiter/Engine]]
