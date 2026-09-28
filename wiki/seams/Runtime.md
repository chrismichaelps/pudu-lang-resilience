---
type: seam
capacity: BACKBONE
tags: [seam, backbone]
---

# Runtime (seam)

## Classification

Effect boundary between strategies and the world: `Strategy.Runtime` holds the pipeline's
[[src/PuduLangResilience/Clock|clock]], [[src/PuduLangResilience/Randomizer|randomizer]], and
[[src/PuduLangResilience/Telemetry|telemetry reporter]], and is handed to every strategy on
`attach` and on each execution.

## Adapters

- **System** — `Clock.system()` and `Randomizer.system()`, the pipeline defaults.
- **Manual** — `Clock.ofManual`, `Randomizer.seeded`, and `Randomizer.fixed`, used by every
  timing and chance test and available to package users.

## Health

No strategy sleeps, reads time, or draws randomness except through the runtime. Timeouts and
cancellable pauses still use `Std.Concurrent.Cancel` deadlines on the system clock
([[decisions/ADR-0003-cooperative-cancellation]]).

## Referenced by

[[seams/_MOC]] · [[grammar/pudu]] · [[src/PuduLangResilience/Clock]] · [[decisions/ADR-0002-injected-runtime]]
