---
type: module
path: "@root/src/PuduLangResilience/Pipeline.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, backbone]
aliases: [PuduLangResilience.Pipeline]
---

# PuduLangResilience.Pipeline

## Purpose

Composes strategies into one executable policy, validates them, and reports each execution's start and end.

## Interface

### Signatures

```pudu
export type Options = {
  name: Option[Str],
  instance: Option[Str],
  clock: Clock.Clock,
  randomizer: Randomizer.Randomizer,
  listeners: Array[Telemetry.Listener],
  severityOf: Option[fn(Telemetry.Event) -> Telemetry.Severity]
}

export type Pipeline[T, E] = {
  name: Option[Str],
  instance: Option[Str],
  strategies: Array[Strategy.Strategy[T, E]],
  clock: Clock.Clock,
  telemetry: Telemetry.Reporter,
  run: fn(Context.Context, Strategy.Callback[T, E]) -> Resilience.Outcome[T, E]
}

export type Invalid = { problems: Array[Str] }

export type Descriptor = { name: Option[Str], instance: Option[Str], strategies: Array[Described] }

export type Described = { name: Option[Str], kind: Str, summary: Str }

export fn defaults() -> Options

export fn build[T, E](strategies: Array[Strategy.Strategy[T, E]]) -> Result[Pipeline[T, E], Invalid]

export fn buildWith[T, E](options: &Options, strategies: Array[Strategy.Strategy[T, E]]) -> Result[Pipeline[T, E], Invalid]

export fn empty[T, E]() -> Pipeline[T, E]

export fn execute[T, E](pipeline: &Pipeline[T, E], callback: Strategy.Callback[T, E]) -> Resilience.Outcome[T, E]

export fn executeWith[T, E](pipeline: &Pipeline[T, E], context: Context.Context, callback: Strategy.Callback[T, E]) -> Resilience.Outcome[T, E]

export fn run[T, E](pipeline: &Pipeline[T, E], action: fn(Context.Context) -> Result[T, E]) -> Resilience.Outcome[T, E]

export fn runWith[T, E](pipeline: &Pipeline[T, E], context: Context.Context, action: fn(Context.Context) -> Result[T, E]) -> Resilience.Outcome[T, E]

export fn asStrategy[T, E](pipeline: &Pipeline[T, E]) -> Strategy.Strategy[T, E]

export fn describe[T, E](pipeline: &Pipeline[T, E]) -> Descriptor

export fn explain(invalid: &Invalid) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangResilience/Clock]], [[src/PuduLangResilience/Constants/Events]], [[src/PuduLangResilience/Constants/Messages]], [[src/PuduLangResilience/Context]], [[src/PuduLangResilience/Randomizer]], [[src/PuduLangResilience]], [[src/PuduLangResilience/Strategy]], [[src/PuduLangResilience/Telemetry]], [[src/PuduLangResilience/Utils/Template]], `Std.List`, `Std.Option`.
- **Consumed by:** [[src/PuduLangResilience/Registry]].

## Algorithm

1. `buildWith` collects every strategy's problems (prefixed by its name or kind) and every name used twice; any problem refuses the build.
2. Otherwise each strategy is attached and the layers are composed once, outermost first.
3. `executeWith` reports `PipelineExecuting`, runs the composed layers, and reports `PipelineExecuted` with the duration on the pipeline clock and the outcome, at `Information` for a value and `Warning` for a failure.
4. `run` lifts a plain `Result` callback; `asStrategy` nests a pipeline, which keeps its own clock and telemetry.

## Negative Logic (Prohibited Paths)

- No execution runs through a pipeline with an invalid strategy.

## Edge Cases

- Unnamed strategies may repeat; named ones may not.
- An empty pipeline runs the callback exactly once.

## Depth

DEPTH 0.8 (DEEP). Tested by the suite mirroring this module under `test/`.

## Grill Log

- **Q:** Why an array of strategies instead of a builder?
  **A:** The pipeline reads top to bottom as its layers. _Rejected:_ `builder().add(a).add(b)` (same information, more ceremony).
- **Q:** Why collect problems instead of failing at the first?
  **A:** One build reports everything wrong with its configuration. _Rejected:_ first-problem errors.

## Referenced by

[[src/PuduLangResilience/_MOC]] · [[src/PuduLangResilience/Registry]]
