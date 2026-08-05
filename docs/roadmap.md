# Roadmap

## Done

- **PQL front-end** — Lexer, parser and typed AST for the PuppetDB entities (`nodes`, `resources`, `facts`, `inventory`, `catalogs`, `reports`, `events`, `edges`, `fact_contents`, `fact_paths`, `factsets`, `environments`, `packages`, `package_inventory`), the comparison / regexp (`~` `!~` `~>`) / boolean operators, `in` membership, null tests, projection with aggregate/transform **functions** (`count`, `avg`, `sum`, `min`, `max`, `to_string`), **`group by`**, the legacy `select_*` subquery spelling, and `order` / `limit` / `offset`.
- **AST-query compiler & parser** — Compile PQL to PuppetDB's canonical `["from", entity, ["and", …]]` JSON wire form via `MarshalAST`; `ParseAST` reads that JSON back into a `Query` (byte-exact round-trip).
- **In-memory evaluator** — `NewStore` with recursive subquery evaluation, `group by` + `count`/`avg`/`sum`/`min`/`max` aggregation, `~>` array matching, ordering, paging and projection — no server required, fully testable.
- **HTTP client** — `NewClient` POSTs PQL or a compiled AST to a live `/pdb/query/v4`, with token auth, behind an injectable `http.RoundTripper` seam.
- **HTTP server & command ingestion** — `NewServer` serves `/pdb/query/v4` (PQL + AST + per-entity paths) and ingests commands at `/pdb/cmd/v1`; `Store.Ingest` handles replace facts (v5), replace catalog (v9) and store report (v8), expanding each into the derived query entities.
- **Embedded storage** — pure-Go JSON-file backend (`Open` / `Save` / `Snapshot` / `Load`), no external database.

## Residuals (named, not silently capped)

- `to_string(field, fmt)` and implicit subqueries parse and compile (the client sends them to a real PuppetDB) but return a clear error in the in-memory evaluator.
- Older command wire-format versions; PuppetDB-identical catalog/report content hashes (the store uses deterministic SHA-1 hashes); the `latest_report_*` node rollups; and the query API's pagination/count HTTP headers.

Quality is a standing gate: 100% coverage including error branches, `gofmt` + `go vet` clean, CI green across the six 64-bit Go targets (amd64, arm64, riscv64, loong64, ppc64le, s390x).
