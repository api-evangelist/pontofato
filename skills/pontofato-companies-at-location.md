---
name: Find companies at and around a Brazilian location
description: >-
  Join Receita Federal establishment data onto CNEFE geometry — companies registered at a CEP,
  CEPs within a radius, and business activity by CNAE around a point.
api: openapi/pontofato-openapi-original.json
operations: [empresas, raio, vizinhanca]
generated: '2026-09-05'
method: generated
---

# Companies at a location

Base: `https://pontofato.com`. `empresas` and `raio` are free; `vizinhanca` has 10 free
calls/day per IP, then costs $0.05 per call (see the x402 payment skill).

1. **Companies registered at a CEP:** `empresas` — `GET /api/empresas?cep=<cep>` (optional
   `page`, 0-based, 50 per page). Each `Empresa` carries CNPJ, razão social, nome fantasia,
   CNAE and situação cadastral; active establishments sort first, closed ones are included
   because the history of the place is part of the fact.
2. **CEPs around a point:** `raio` — `GET /api/raio` with either `cep=<center>` or
   `lat`+`lon`, and `raio` in meters (1–2000). Returns each CEP with its distance and CNEFE
   point count.
3. **Business activity in a radius:** `vizinhanca` — `GET /api/vizinhanca` with the same
   center options plus optional `cnae` (2, 5 or 7-digit prefix) and `desde` (ISO date,
   default 90 days before the base date). Returns totals (ativas, abertas, baixadas), counts
   per CNAE and a sample of recent openings with distances. Watch the `x-cota-gratis`
   response header (e.g. `3/10`) for remaining free quota; when exhausted the call answers
   HTTP 402 instead of 429.

Receita data is reloaded twice a week; every `vizinhanca` response includes `base.dump_date`
so you can cite the vintage.
