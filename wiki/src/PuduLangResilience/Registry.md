---
type: module
path: "@root/src/PuduLangResilience/Registry.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module]
aliases: [PuduLangResilience.Registry]
---

# PuduLangResilience.Registry

## Purpose

Named pipelines built once from a registered builder, shared by every caller, and rebuilt on `reload`.

## Interface

### Signatures

```pudu
export type BuilderContext = { key: Str, builderName: Str, instanceName: Option[Str] }

export type Builder[T, E] = fn(BuilderContext) -> Array[Strategy.Strategy[T, E]]

export type Options = {
  pipeline: Pipeline.Options,
  builderName: fn(Str) -> Str,
  instanceName: fn(Str) -> Option[Str]
}

export type RegistryError = NotFound(Str) | Invalid(Str, Pipeline.Invalid)

export type Registry[T, E] = {
  options: Options,
  entries: Shared.Shared[Map[Str, Entry[T, E]]]
}

export fn defaults() -> Options

export fn create[T, E]() -> Registry[T, E]

export fn createWith[T, E](options: Options) -> Registry[T, E]

export fn tryAddBuilder[T, E](registry: &Registry[T, E], key: Str, builder: Builder[T, E]) -> Bool

export fn get[T, E](registry: &Registry[T, E], key: Str) -> Result[Pipeline.Pipeline[T, E], RegistryError]

export fn tryGet[T, E](registry: &Registry[T, E], key: Str) -> Option[Pipeline.Pipeline[T, E]]

export fn getOrAdd[T, E](registry: &Registry[T, E], key: Str, builder: Builder[T, E]) -> Result[Pipeline.Pipeline[T, E], RegistryError]

export fn reload[T, E](registry: &Registry[T, E], key: Str) -> Result[Pipeline.Pipeline[T, E], RegistryError]

export fn keys[T, E](registry: &Registry[T, E]) -> Array[Str]

export fn explain(problem: &RegistryError) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Constants/Messages]], [[src/PuduLangResilience/Pipeline]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Utils/Shared]], [[src/PuduLangResilience/Utils/Template]], `Std.Map`.
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. An entry holds a builder and the pipeline built from it, if any, in shared state.
2. `get` builds under the lock on first use, so concurrent callers share one pipeline.
3. `reload` builds again and replaces the stored pipeline; state such as an open circuit starts afresh.
4. The builder receives the key and the names the registry options derive from it.

## Negative Logic (Prohibited Paths)

- An invalid pipeline is never stored; `get` answers `Invalid` each time until the builder is fixed.

## Edge Cases

- `tryAddBuilder` never replaces an existing builder.
- `tryGet` answers nothing for an unknown or invalid key.

## Depth

DEPTH 0.7 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why `Str` keys?
  **A:** Keys name dependencies; a generic key type adds a bound for no practical gain. _Rejected:_ `Registry[K, T, E]`.
- **Q:** Why explicit `reload` instead of change tokens?
  **A:** Configuration sources differ by program; the caller knows when its configuration changed. _Rejected:_ a watcher inside the registry.

## Referenced by

[[CHANGELOG]] · [[domain/Pipeline]] · [[src/PuduLangResilience/Constants/Messages]] · [[src/PuduLangResilience/Pipeline]] · [[src/PuduLangResilience/Strategy]] · [[src/PuduLangResilience/Utils/Shared]] · [[src/PuduLangResilience/Utils/Template]] · [[src/PuduLangResilience/_MOC]]
