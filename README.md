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

Then tag a release and bump it in the consumers:

```bash
git tag v0.x.y && git push --tags
go get github.com/nokku-sh/protos@v0.x.y   # in nokku, nokkud, nk
```

For local work across repos, point the consumers at this checkout with a
`go.work` in the parent directory (not committed):

```bash
go work init ./nokku ./nokkud ./nk ./protos ./mon
```

CI fails when `gen/` is stale.
