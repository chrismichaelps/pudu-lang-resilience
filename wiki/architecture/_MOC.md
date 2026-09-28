---
type: moc
tags: [moc, architecture]
aliases: [Architecture]
---

# Architecture

## Shape

A caller builds a [[src/PuduLangResilience/Pipeline|pipeline]] from an array of
[[src/PuduLangResilience/Strategy|strategies]], outermost first, and runs callbacks through it. Each
strategy wraps the next; the innermost wraps the callback. Every layer answers an `Outcome`, so a
rejection by one layer is a value the layers outside it judge like any other outcome.

Effects reach strategies only through the [[seams/Runtime]] the pipeline lends them: its clock,
its randomizer, and its telemetry reporter. State that outlives one call (a circuit, a limiter's
permits, a registry's pipelines) lives in [[src/PuduLangResilience/Utils/Shared]] values and is
changed only under their locks.

## Layers

| Layer | Holds | May import |
| --- | --- | --- |
| `Constants/` | event names and message templates | nothing |
| `Utils/` | saturating numbers, shared state, templates | std, Constants |
| `Domain/` | pure backoff, health, circuit, permit, and limiter arithmetic | Utils, std |
| public modules | the root vocabulary, context, pipeline, strategies, limiters, chaos, registry, telemetry | Domain, Utils, Constants, each other, std |

`Domain/` performs no effects and imports no public module. Public modules import one another
only toward the core: strategies use `Pipeline`'s types through `Strategy`, never the reverse.

## Pages

- [[architecture/LANGUAGE]] — the vocabulary every page uses.
- [[architecture/TESTING]] — the test levels and what each proves.
- [[grammar/pudu]] — the language rules the code follows.

## Referenced by

[[00-INDEX]] · [[grammar/pudu]]
