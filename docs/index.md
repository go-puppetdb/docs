# go-puppetdb

**PuppetDB query language (PQL) in pure Go — parser, in-memory evaluator and /pdb/query/v4 client.**

go-puppetdb is a pragmatic, pure-Go (CGO_ENABLED=0) toolkit for PuppetDB's query language. It provides a PQL lexer, parser and typed AST; a compiler from that AST to PuppetDB's canonical AST-query JSON wire form; an in-memory evaluator so PQL is useful and fully testable without a server; and an HTTP client for a real PuppetDB /pdb/query/v4 endpoint behind an injectable http.RoundTripper seam. Entities (nodes, resources, facts, inventory, catalogs, reports, …), comparison / regexp / boolean operators, in subqueries, null tests, projection and order / limit / offset are all supported. Standard library only, 100% coverage, six arches and WebAssembly.

- **[Why pure Go](why.md)** — a static, cgo-free engine for the Puppet stack.
- **[PQL reference](pql.md)** — the capabilities in detail.
- **[Usage & API](api.md)** — the Go API and how to call it.
- **[Roadmap](roadmap.md)** — what is done and what is next.

## Guarantees

- **Pure Go, zero cgo.** Imports the Go standard library only; cross-compiles to the six 64-bit Go targets (amd64, arm64, riscv64, loong64, ppc64le, s390x) and WebAssembly, linking into a static binary.
- **Faithful to PuppetDB's PQL and AST-query wire form.**
- **100% test coverage** including error branches, enforced as a CI gate.
