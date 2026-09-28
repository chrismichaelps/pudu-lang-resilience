---
type: grammar
language: Pudu
version: "0.1.2"
tags: [grammar]
aliases: [Grammar — Pudu, Pudu Grammar]
---

# Grammar — Pudu

The Pudu surface this repository is written against, pinned to compiler `0.1.2` as published in
its release archive. Where this page and the compiler disagree, the compiler wins and this page is
corrected in the same change.

## SDK Discovery Map

| Need | Module | Entry points |
| --- | --- | --- |
| Threads | `Std.Concurrent` | `start`, `join`, `sleep`, `contain` |
| Futures | `Std.Concurrent.Future` | `start`, `wait`, `isDone` |
| Cancellation | `Std.Concurrent.Cancel` | `token`, `child`, `childExpiring`, `cancel`, `reason`, `stopped`, `check`, `pause`, `explain` |
| Shared state | `Std.Sync` | `mutex`, `withLock`, `cell`, `get`, `set`, `swap`, `counter`, `increment` |
| Channels | `Std.Channel` | `channel`, `send`, `receive`, `pending` |
| Monotonic time | `Std.Time` | `elapsed` (milliseconds since an arbitrary origin) |
| Randomness | `Std.Random` | `fromSeed`, `fromClock`, `below` |
| Exact ratios | `Std.Decimal` | `fromInt`, `toInt`, `toFloat64`, `parse`, `divide`, `floor` |
| Integer bounds | `Std.Math` | `min`, `max` |
| Float math | `Std.Math.Float` | `powf`, `tanh`, `sqrt`, `floor`, `isFinite` |
| Collections | `Std.List`, `Std.Map` | `List.get`, `List.first`; `Map.get`, `Map.insert`, `Map.remove`, `Map.keys` |
| Tests | `Std.Test` | `suite`, `equals`, `that`, `not`, `present`, `absent`, `run`, `failuresOf`, `report` |

## Imports / Namespaces

- One module per file; the module name is the path under its source root with `/` as `.`:
  `src/PuduLangResilience/Domain/Backoff.pudu` is `module PuduLangResilience.Domain.Backoff`.
- Every import is qualified and aliased: `import Std.Sync as Sync`. Nothing is imported implicitly.
- Suites under `test/` and programs under `examples/` import package modules through the
  manifest's source root.

## Core Primitives

- Records: `export type Point = { x: Int, y: Int }`, built as `Point{x: 1, y: 2}`, updated as
  `Point{..p, y: 5}`. An imported record is updated with its qualified name:
  `Retry.Options{..Retry.defaults(), maxRetryAttempts: 5}`.
- Sum types: `export type Id = NumberId(Int) | TextId(Str)`, taken apart with `match`.
- Generic type aliases name function types: `export type Callback[T, E] = fn(Context) -> Outcome[T, E]`.
- A function stored in a record field is called as `(record.field)(argument)`.
- A generic trait implementation declares its parameters after `impl`:
  `impl [T] Chaining[T] for Chain[T] { … }`.
- `show(value)` and `==` work on values of any type, including type parameters.
- `Option[T]` and `Result[T, E]` helpers are module functions (`Option.unwrapOr(value, fallback)`).
- `?` propagates `None` or `Err` from a function whose return type is the same family.
- Module scope holds only `const`. Lookup tables are `const` tables built with `mapOf([...])`.
- Closures: `fn(x: Int) -> Int { x + 1 }`, or the short form `|x: Int| x + 1`.
- A closure captures a copy of every binding it names. State that callbacks, threads, or later
  calls must observe lives in a `Sync.Cell`, guarded by a `Sync.Mutex` when a write depends on a
  read.
- Borrowing: `&T` parameters are read-only views; `*view` copies a borrowed value into an owned one.

## Numbers

- There is no built-in conversion between `Int` and `Float`. `Decimal.toFloat64(Decimal.fromInt(n))`
  converts an integer to a float; [[src/PuduLangResilience/Utils/Numeric]] converts back.
- Ratios in the public surface are `Decimal` literals (`0.1d`), compared exactly. Floats appear
  only inside the exponential jitter formula.
- Durations are `Int` milliseconds everywhere.

## Architectural Laws

- Dependency direction is inward: the public modules at `PuduLangResilience.*` use
  `PuduLangResilience.Domain.*`, which uses `Utils` and `Constants`. `Domain` never imports a public
  module and performs no effects.
- Every effect a strategy performs (clock, sleep, randomness, telemetry) reaches it through the
  [[seams/Runtime]] record the pipeline hands it, so strategies are tested with a manual clock and
  a seeded randomizer.
- Failures are values: every callback answers an `Outcome`, and every rejection a strategy makes is
  a `Failure` variant. Nothing is thrown.
- Every module the package ships is `PuduLangResilience` or lives under `src/PuduLangResilience/`,
  so a program that installs the package keeps every other module name.

## Syntax Rules / Naming

- Types, traits, modules, and variants are `PascalCase`; values `camelCase`; constants
  `UPPER_SNAKE_CASE`.
- Every file header and exported type carries the FMCF anchor, one line:
  `/** @Namespace.Entity.Role — intent */`, five to eight words of intent.
- Every `fn`, `export fn`, and `const` carries a `///` doc comment of one or two lines stating
  what it answers or holds, in the voice of the standard library's own documentation. It states
  the contract, not the steps.
- Rationale belongs in the mirrored page's Grill Log. No narration, history, or explanation of the
  obvious in code.

## Prohibited Patterns (verified against the 0.1.2 compiler)

- **A brace inside a string literal is interpolation.** A literal brace is `\{` or `\}`.
- **`Array.get(i)` and `items[i]` stop the program when `i` is out of range.** Use `List.get` or
  `List.first` for an `Option`.
- **`scope`, `module`, `where`, and `task` are keywords**; none can name a binding or a field.
- **`task` is reserved as well**; a binding for a started thread is `started` or `worker`.
- **Matching a borrowed value binds its parts as owned values.** `case Raised(problem) => show(problem)`
  over `&Failure[E]`; writing `*problem` is an error.
- **A unit value is matched as `Ok(_)`, not `Ok(())`.**
- **A comparison that picks the smaller or larger of two values is `Math.min` or `Math.max`**, not
  an `if`: the `>` against `>=` choice there cannot change the answer, and mutation testing reports
  it as a survivor.
- **`&-1` lexes as the operator `&-`**; borrow a negative literal through a named constant.
- **A write that depends on a read of a `Sync.Cell` without the cell's mutex** loses updates
  under concurrent use.
- **Sleeping with `Std.Concurrent.sleep` inside a strategy** bypasses the pipeline clock; sleep
  through the [[seams/Runtime]] clock.

## Senior Definition Needed

(none open)

## Referenced by

[[00-INDEX]] · [[architecture/_MOC]] · [[src/PuduLangResilience]] · [[src/PuduLangResilience/Chaos]] · [[src/PuduLangResilience/Chaos/Behavior]] · [[src/PuduLangResilience/Chaos/Fault]] · [[src/PuduLangResilience/Chaos/Latency]] · [[src/PuduLangResilience/Chaos/Outcome]] · [[src/PuduLangResilience/Chaos/Weighted]] · [[src/PuduLangResilience/CircuitBreaker]] · [[src/PuduLangResilience/Clock]] · [[src/PuduLangResilience/Constants/Events]] · [[src/PuduLangResilience/Constants/Messages]] · [[src/PuduLangResilience/Context]] · [[src/PuduLangResilience/Domain/Algorithms]] · [[src/PuduLangResilience/Domain/Backoff]] · [[src/PuduLangResilience/Domain/Circuit]] · [[src/PuduLangResilience/Domain/Health]] · [[src/PuduLangResilience/Domain/Permits]] · [[src/PuduLangResilience/Fallback]] · [[src/PuduLangResilience/Hedging]] · [[src/PuduLangResilience/Hedging/Attempts]] · [[src/PuduLangResilience/Limiter]] · [[src/PuduLangResilience/Limiter/Concurrency]] · [[src/PuduLangResilience/Limiter/Engine]] · [[src/PuduLangResilience/Limiter/FixedWindow]] · [[src/PuduLangResilience/Limiter/Partitioned]] · [[src/PuduLangResilience/Limiter/SlidingWindow]] · [[src/PuduLangResilience/Limiter/TokenBucket]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Predicate]] · [[src/PuduLangResilience/Randomizer]] · [[src/PuduLangResilience/RateLimiter]] · [[src/PuduLangResilience/Registry]] · [[src/PuduLangResilience/Retry]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Telemetry]] · [[src/PuduLangResilience/Telemetry/Log]] · [[src/PuduLangResilience/Telemetry/Meter]] · [[src/PuduLangResilience/Timeout]] · [[src/PuduLangResilience/Utils/Numeric]] · [[src/PuduLangResilience/Utils/Shared]] · [[src/PuduLangResilience/Utils/Template]] · [[tools/Mutate]]
