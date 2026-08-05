# Usage & API

```go
import "github.com/go-puppetdb/puppetdb"
```

```go
q, _ := puppetdb.Parse(`nodes[certname]{ certname in resources[certname]{ type = "Class" and title = "nginx" } } order by certname limit 10`)

// Compile to the canonical AST-query JSON the HTTP API accepts:
fmt.Println(string(q.MarshalAST()))

// Evaluate against an in-memory dataset:
store := puppetdb.NewStore()
store.Add("nodes", puppetdb.Row{"certname": "web1"})
store.Add("resources", puppetdb.Row{"certname": "web1", "type": "Class", "title": "nginx"})
rows, _ := store.Eval(q)

// Or query a live PuppetDB:
c := puppetdb.NewClient("https://puppetdb.example:8081", puppetdb.WithToken(token))
rows, _ = c.Query(context.Background(), `nodes{ facts.os.family = "RedHat" }`)
```

`Parse` turns PQL into a typed AST; `MarshalAST` compiles it to PuppetDB's canonical `["from", …]` JSON, and `ParseAST` reads that JSON back into a `Query`. `NewStore` gives an in-memory dataset you populate with `Add` and query with `Eval`, including recursive subqueries, `group by` + `count`/`avg`/`sum`/`min`/`max` aggregation, ordering, paging and projection. `NewClient` (with `WithToken` and a custom transport) POSTs PQL or a compiled AST to a live `/pdb/query/v4` endpoint.

## Serve your own PuppetDB endpoint

The library can also back a PuppetDB-compatible endpoint from a pure-Go embedded store — no external database:

```go
db, _ := puppetdb.Open("puppetdb.json") // pure-Go persistence
srv := puppetdb.NewServer(db)           // /pdb/query/v4 + /pdb/cmd/v1
http.ListenAndServe(":8081", srv)

// Agents POST commands to /pdb/cmd/v1?command=replace_facts&version=5&certname=…
// (replace facts v5, replace catalog v9, store report v8); the ingested data is
// queryable at once. Clients POST PQL or AST to /pdb/query/v4.
```

## Command line & builds

The library is `CGO_ENABLED=0` pure Go. Cross-compile it anywhere:

```sh
GOOS=linux   GOARCH=arm64    go build ./...
GOOS=js      GOARCH=wasm     go build ./...
```

It builds and tests on all six 64-bit Go targets (amd64, arm64, riscv64, loong64, ppc64le, s390x).
