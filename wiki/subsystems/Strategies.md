---
type: subsystem
tags: [subsystem]
---

# Strategies

Reactive: [[src/PuduLangResilience/Retry]], [[src/PuduLangResilience/CircuitBreaker]],
[[src/PuduLangResilience/Fallback]], [[src/PuduLangResilience/Hedging]]. Proactive:
[[src/PuduLangResilience/Timeout]], [[src/PuduLangResilience/RateLimiter]]. All share
[[src/PuduLangResilience/Predicate]] and [[src/PuduLangResilience/Strategy]]; the pure rules sit
in [[src/PuduLangResilience/Domain/_MOC]].

## Referenced by

[[subsystems/_MOC]]
