---
type: architecture
tags: [architecture, test]
aliases: [Testing]
---

# Testing

Every suite is a file under `test/`, mirroring the module it covers; `pudu test test` runs them all
and each suite names its failed checks on stderr.

| Level | Suites | What they prove |
| --- | --- | --- |
| Domain | `test/PuduLangResilience/Domain/**`, `test/PuduLangResilience/Utils/**` | every rule of the pure modules: delays, jitter, saturation, health windows, circuit transitions, permit queues, each limiter algorithm |
| Strategy | one suite per strategy and core module | success, handled and unhandled failures, cancellation, telemetry, validation bounds, on a manual clock and a fixed randomizer where time or chance matters |
| Concurrency | `Limiter/LimiterTest`, `HedgingTest`, `Utils/UtilsTest` | real threads: queued waiters, eviction, cancellation while queued, losing attempts cancelled and joined, no lost updates |
| Package | `test/Package/LayoutTest` | every shipped module is the root `PuduLangResilience` or under it, is named after its path, and agrees with the manifest |
| Integration | `test/Integration/PipelineScenarioTest` | every strategy stacked in one pipeline, and their interactions |
| Examples | `examples/*.pudu`, run by CI | the documented programs compile and answer 0 |
| Mutation | [[tools/Mutate]] | the suites notice single-point changes to the pure layer |

The mutation gate runs on pull requests over `Domain/` with a threshold of 100: every valid mutant
is killed. A mutant that cannot change behaviour is removed by simplifying the code rather than
excused.

## Referenced by

[[architecture/_MOC]] · [[tools/Mutate]]
