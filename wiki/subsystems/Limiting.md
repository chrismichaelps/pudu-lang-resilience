---
type: subsystem
tags: [subsystem]
---

# Limiting

[[src/PuduLangResilience/Limiter]] defines leases; [[src/PuduLangResilience/Limiter/Engine]] runs
[[src/PuduLangResilience/Domain/Permits]] over one of the
[[src/PuduLangResilience/Domain/Algorithms]]; the kinds under
[[src/PuduLangResilience/Limiter/_MOC]] validate their options and build engines; the
[[src/PuduLangResilience/RateLimiter]] strategy consumes any of them through [[seams/Limiter]].

## Referenced by

[[subsystems/_MOC]]
