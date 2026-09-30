# protos

The Nokku API schema and the Go code generated from it. Everything that
talks to the Nokku API (`nokku`, `nokkud`, `nk`) imports this module, so all
of them compile against the same schema at the version their `go.mod` pins.

- `nokku/` is the buf module. Proto package `nokku.v1`.
- `gen/` is the generated Go and connect code. Never edit it by hand.

The web UI in `nokku` generates its TypeScript client from the `.proto`
files of the exact version its `go.mod` pins, so Go and TS never drift.

## Access rules

Every RPC declares who may call it with `option (access)`, see
`nokku/nokku/v1/access.proto`. An RPC without it is denied by the server.

## Changing the schema

```bash
task lint   # buf format and lint
task gen    # regenerate gen/, must be committed
```

CI fails when `gen/` is stale.

## Local development across repos

Keep all repos checked out side by side with a `go.work` in the parent
directory. The parent is not a git repo, so the file is never committed:

```bash
cd ~/Projects/nokku-sh
go work init ./mon ./nk ./nokku ./nokkud ./protos
```

With it, nokku, nokkud and nk build against your local `protos` and `mon`
checkouts. A proto edit plus `task gen` here is visible in all of them right
away, and `task gen` in nokku builds the TS client from the same local
protos.

CI never sees the `go.work`. Each pipeline checks out one repo and uses the
versions pinned in its `go.mod`. So a proto change that works locally fails
CI until it is released. The same goes for `mon`, which nk and nokkud pin
too:

1. Commit and push the proto change with its regenerated `gen/`.
2. Tag it. Tags must be annotated: `git tag -a v0.x.y -m v0.x.y && git push --follow-tags`
3. In each consumer: `go get github.com/nokku-sh/protos@v0.x.y`, and
   `task gen` in nokku for the TS client.

To see what CI will see, run with `GOWORK=off`, for example
`GOWORK=off go build ./...`. `go mod tidy` writes the same `go.mod` with or
without the workspace.

Right after a tag, `proxy.golang.org` can briefly answer "unknown revision",
especially if something asked for the version before it existed. It clears
within about 30 minutes. To go around it:

```bash
GOPROXY=direct GONOSUMDB=github.com/nokku-sh/protos go get github.com/nokku-sh/protos@v0.x.y
```
