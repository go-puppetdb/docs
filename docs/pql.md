# PQL reference

This page walks the engine's capabilities. Each is complete unless explicitly marked as a documented deferral.

## PQL front-end

Lexer, parser and typed AST for the PuppetDB entities (`nodes`, `resources`, `facts`, `inventory`, `catalogs`, `reports`, `events`, `edges`, `fact_contents`), comparison / regexp / boolean operators, `in` membership, null tests, projection and `order` / `limit` / `offset`.

## AST-query compiler

Compile PQL to PuppetDB's canonical `["from", entity, ["and", …]]` JSON wire form via `MarshalAST`, the shape the HTTP API accepts directly.

## In-memory evaluator

`NewStore` with recursive subquery evaluation, ordering, paging and projection, so a PQL query runs against an in-memory dataset — no server required, fully testable.

## HTTP client

`NewClient` POSTs PQL or a compiled AST to a live `/pdb/query/v4`, with token auth, behind an injectable `http.RoundTripper` seam.

## Storage server & aggregates _( planned )_

This library queries; it does not persist, index or serve. Aggregate / function projections (`count()`, `group by`), legacy `select_*` subquery spellings and live-data sync are documented follow-ons.
