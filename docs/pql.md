# PQL reference

This page walks the engine's capabilities. Each is complete unless explicitly marked as a documented deferral.

## PQL front-end

Lexer, parser and typed AST for the PuppetDB entities (`nodes`, `resources`, `facts`, `inventory`, `catalogs`, `reports`, `events`, `edges`, `fact_contents`, `fact_paths`, `factsets`, `environments`, `packages`, `package_inventory`). The parser accepts every construct of PuppetDB's PQL grammar:

- **Comparison** `=` `!=` `<` `>` `<=` `>=`.
- **Regexp** `~` (match), `!~` (non-match) and `~>` (regexp-array match).
- **Boolean** `and`, `or`, `not`, grouping with `(` `)`.
- **Membership** `in` against an array literal `[...]` or a subquery.
- **Subqueries** — explicit `in`/`from`, implicit `entity { ... }`, and the legacy `select_<entity>` spelling (all accepted, all compiled to the canonical form).
- **Null tests** `is null`, `is not null`.
- **Projection** — extract lists, dotted deep paths (`facts.os.family`) and aggregate/transform **functions** `count()`, `count(field)`, `avg`, `sum`, `min`, `max`, `to_string(field, fmt)`.
- **Grouping** — `group by` (fields or functions), accepted inside the braces (PQL grammar) and, as a superset, after them.
- **Modifiers** `order by … [asc|desc]`, `limit`, `offset` — inside or after the braces.

## AST-query compiler

Compile PQL to PuppetDB's canonical `["from", entity, ["and", …]]` JSON wire form via `MarshalAST`, the shape the HTTP API accepts directly. `ParseAST` is the inverse — it reads that JSON back into a typed `Query`, byte-exact round-trip.

## In-memory evaluator

`NewStore` runs a parsed PQL query against a registered dataset: filter, recursive `in`-subquery evaluation, `group by` with `count`/`avg`/`sum`/`min`/`max` aggregation, `~>` array matching, ordering, paging and projection — no server required, fully testable. Two constructs parse and compile correctly (so the client sends them to a real PuppetDB) but return a clear error in-memory rather than guessing: `to_string(field, fmt)` (needs PostgreSQL `to_char`) and implicit subqueries (need PuppetDB's entity join graph).

## HTTP client

`NewClient` POSTs PQL or a compiled AST to a live `/pdb/query/v4`, with token auth, behind an injectable `http.RoundTripper` seam.

## HTTP server, command ingestion & embedded storage

`NewServer` serves the query API at `/pdb/query/v4` (PQL and AST, plus the per-entity paths) and ingests commands at `/pdb/cmd/v1`, backed by a `Store` and safe to serve concurrently. `Store.Ingest` accepts the current PuppetDB command wire formats — replace facts (v5), replace catalog (v9), store report (v8) — expanding each into the derived query entities so the data is immediately queryable. `Open` / `Save` / `Snapshot` / `Load` persist a store to a JSON file with no external database.

The remaining PuppetDB-parity items are named explicitly (not silently capped): older command wire-format versions, PuppetDB-identical catalog/report content hashes (the store uses deterministic SHA-1 hashes), the `latest_report_*` node rollups, and the query API's pagination/count HTTP headers.
