---
type: domain
tags: [domain]
---

# Circuit

A dependency's health as a circuit breaker sees it: closed (admitting and counting), open (rejecting until its break ends), half-open (admitting one probe), or isolated (rejecting until closed by hand). Counts roll over a sampling period in ten windows. See [[src/PuduLangResilience/Domain/Circuit]] and [[src/PuduLangResilience/Domain/Health]].

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]]
