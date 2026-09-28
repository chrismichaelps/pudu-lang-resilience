---
type: domain
tags: [domain]
---

# Outcome

The answer of every callback and strategy: `Ok(value)` or `Err(failure)`. A failure is the callback's own error (`Raised`), a rejection (`TimedOut`, `BrokenCircuit`, `IsolatedCircuit`, `RateLimited`), a `Cancelled` execution, or a `Crashed` thread. Default predicates handle every failure except a cancellation, and no value. See [[src/PuduLangResilience]].

## Referenced by

[[domain/_MOC]] · [[architecture/LANGUAGE]]
