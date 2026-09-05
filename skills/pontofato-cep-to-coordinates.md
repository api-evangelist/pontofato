---
name: Resolve a Brazilian CEP to official IBGE coordinates
description: >-
  Turn a CEP (postal code) or street text into CNEFE census points with real IBGE lat/lon —
  not a map approximation — using PontoFato's free, keyless endpoints.
api: openapi/pontofato-openapi-original.json
operations: [cep, unidades, buscar, proximo]
generated: '2026-09-05'
method: generated
---

# Resolve a CEP to coordinates

All operations here are free and need no key. Base: `https://pontofato.com`. Responses are
JSON with CORS `*`. Errors come back as `{"ok": false, "code": "...", "error": "..."}`.

1. **Have a CEP?** Call `cep` — `GET /api/cep/{cep}` (8 digits, hyphen optional). You get
   `pontos[]`, each with `lat`/`lon` recorded by IBGE at the 2022 Census, plus `resumo` and
   coverage. A 400 means a malformed CEP; a 404 means the CEP is outside the loaded base —
   the body's `cobertura` lists which UFs exist.
2. **Need the units inside a building?** Call `unidades` — `GET /api/cep/{cep}/unidades` with
   optional `logradouro` + `numero`, paginated by `limit` (max 50) and `offset`.
3. **Only have street text?** Call `buscar` — `GET /api/buscar?q=<3+ chars>` with optional
   `uf` and `cidade`, then feed a resulting CEP back into step 1.
4. **Only have a coordinate?** Call `proximo` — `GET /api/proximo?lat=..&lon=..` (WGS84;
   there is no default, so never send 0,0) to get the nearest CNEFE point.

The CNEFE base is a fixed Censo 2022 snapshot: addresses created after 2022 will 404 here
even when they exist in the street.
