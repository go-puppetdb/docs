# go-puppetdb

**PuppetDB query language (PQL) in pure Go — parser, in-memory evaluator and /pdb/query/v4 client.**

go-puppetdb is a pragmatic, pure-Go (CGO_ENABLED=0) toolkit for PuppetDB. It provides a PQL lexer, parser and typed AST; a compiler to PuppetDB's canonical AST-query JSON wire form (and `ParseAST`, the inverse); an in-memory evaluator — with `group by` and `count`/`avg`/`sum`/`min`/`max` aggregation — so PQL is useful and fully testable without a server; an HTTP client for a real PuppetDB /pdb/query/v4 endpoint behind an injectable http.RoundTripper seam; command ingestion for the current wire formats (replace facts v5, replace catalog v9, store report v8); a pure-Go embedded storage backend (a JSON file, no external database); and an HTTP server that exposes /pdb/query/v4 and /pdb/cmd/v1 on top of it. Entities (nodes, resources, facts, inventory, catalogs, reports, …), comparison / regexp / boolean operators, in subqueries, null tests, projection and order / limit / offset are all supported. Standard library only, 100% coverage, six arches and WebAssembly.

- **[Why pure Go](why.md)** — a static, cgo-free engine for the Puppet stack.
- **[PQL reference](pql.md)** — the capabilities in detail.
- **[Usage & API](api.md)** — the Go API and how to call it.
- **[Roadmap](roadmap.md)** — what is done and what is next.

## Guarantees

- **Pure Go, zero cgo.** Imports the Go standard library only; cross-compiles to the six 64-bit Go targets (amd64, arm64, riscv64, loong64, ppc64le, s390x) and WebAssembly, linking into a static binary.
- **Faithful to PuppetDB's PQL and AST-query wire form.**
- **100% test coverage** including error branches, enforced as a CI gate.
