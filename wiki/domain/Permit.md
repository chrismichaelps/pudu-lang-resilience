---
type: domain
tags: [domain]
---

# Permit

The unit a limiter grants. A request asks for some permits and is granted, queued up to the queue limit, or denied with a retry time when the limiter knows one. A granted lease hands its permits back once on release. See [[src/PuduLangResilience/Limiter]] and [[src/PuduLangResilience/Domain/Permits]].

## Referenced by

[[domain/_MOC]] · [[architecture/LANGUAGE]]
