# Roadmap

## Done

- **PQL front-end** — Lexer, parser and typed AST for the PuppetDB entities (`nodes`, `resources`, `facts`, `inventory`, `catalogs`, `reports`, `events`, `edges`, `fact_contents`), comparison / regexp / boolean operators, `in` membership, null tests, projection and `order` / `limit` / `offset`.
- **AST-query compiler** — Compile PQL to PuppetDB's canonical `["from", entity, ["and", …]]` JSON wire form via `MarshalAST`, the shape the HTTP API accepts directly.
- **In-memory evaluator** — `NewStore` with recursive subquery evaluation, ordering, paging and projection, so a PQL query runs against an in-memory dataset — no server required, fully testable.
- **HTTP client** — `NewClient` POSTs PQL or a compiled AST to a live `/pdb/query/v4`, with token auth, behind an injectable `http.RoundTripper` seam.

## Next

- **Storage server & aggregates** — This library queries; it does not persist, index or serve. Aggregate / function projections (`count()`, `group by`), legacy `select_*` subquery spellings and live-data sync are documented follow-ons.

Quality is a standing gate: 100% coverage including error branches, `gofmt` + `go vet` clean, CI green across the six 64-bit Go targets (amd64, arm64, riscv64, loong64, ppc64le, s390x).
