# Go

Go's selling point is uniformity: one formatter, one toolchain, one
obvious way. That makes orientation fast — but a handful of subtle
gotchas (nil interfaces, goroutine leaks) catch even experienced
developers.

## Modules & Dependencies

Go modules are built into the toolchain. `go.mod` declares the module
and dependencies; `go.sum` locks hashes.

```bash
cat go.mod                  # module path, Go version, dependencies
go mod download             # fetch dependencies
go mod tidy                 # add missing / remove unused deps (run before PRs)
go get example.com/pkg@v1.2.3   # add/upgrade a dependency
go mod why example.com/pkg  # why is this dependency here?
```

`go mod tidy` keeping `go.mod`/`go.sum` clean is expected before a PR — a
dirty module file is a common review nit. Dependencies are cached
globally; there's no per-project `node_modules` equivalent.

## Build, Run, Test

The toolchain is the whole story:

```bash
go build ./...              # build everything
go run ./cmd/myapp          # build + run
go test ./...               # test everything
go test ./pkg/foo -run TestName -v     # one package, one test, verbose
go test -race ./...         # with the race detector (use it!)
go test -bench=. ./...      # run benchmarks
go test -cover ./...        # coverage
```

`./...` means "this package and all sub-packages." The **race detector**
(`-race`) is one of Go's best features — it finds data races at runtime;
run it in CI.

## Formatting

There is exactly one format, and it's not negotiable — that's the point:

```bash
gofmt -w .                  # format (or goimports, which also fixes imports)
go vet ./...                # catch suspicious constructs
```

`gofmt` ends all formatting debate (no bikeshedding — see
[../12-mindset/bikeshedding.md](../12-mindset/bikeshedding.md)).
**goimports** is the common upgrade — it formats *and* manages import
grouping. Format-on-save is standard.

## Linting

Beyond `go vet`:

| Tool | Role |
|---|---|
| **golangci-lint** | The standard meta-linter; runs many linters at once |
| **staticcheck** | Excellent static analysis (included in golangci-lint) |

```bash
golangci-lint run
staticcheck ./...
```

`golangci-lint` with the project's `.golangci.yml` is what CI usually
runs; mirror it locally.

## Profiling: pprof

Go has best-in-class built-in profiling (connect to
[../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)):

```bash
# From tests
go test -cpuprofile cpu.prof -memprofile mem.prof -bench .
go tool pprof cpu.prof          # interactive; `top`, `list`, `web` (flame graph)

# From a running service (with net/http/pprof imported)
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30
go tool pprof http://localhost:6060/debug/pprof/heap
```

`pprof` plus the race detector cover most performance and concurrency
debugging.

## Classic Gotchas

### The nil interface trap

The most infamous Go gotcha. An interface holding a *typed nil pointer*
is **not** equal to `nil`:

```go
func doThing() error {
    var p *MyError = nil
    return p              // returns a non-nil error! (type is set, value is nil)
}
if doThing() != nil {    // TRUE — surprise; the interface isn't nil
    // ...
}
```

An interface is nil only when *both* its type and value are nil.
Returning a typed nil pointer as an `error` produces a non-nil
interface. Fix: return `nil` literally, not a nil typed pointer.

### Loop variable capture (fixed in Go 1.22+)

Historically, loop variables were shared across iterations, so
goroutines/closures in a loop all captured the *same* variable:

```go
for _, v := range items {
    go func() { use(v) }()   // pre-1.22: all goroutines saw the last v
}
```

**As of Go 1.22, each iteration gets a fresh variable**, fixing this for
range loops. But you'll still see the old defensive `v := v` shadowing in
older code, and code targeting older Go versions still has the bug. Know
which Go version the module targets (`go.mod`).

### Goroutine leaks

A goroutine blocked forever (on a channel no one sends to/receives from)
never exits — a leak that accumulates:

```go
ch := make(chan int)        // unbuffered
go func() { ch <- 1 }()     // blocks forever if no one receives → leak
```

Always ensure goroutines can terminate (a receiver, a `context` for
cancellation, a `select` with a done channel). Leaked goroutines are a
common production memory issue; `pprof`'s goroutine profile finds them.

### Other gotchas worth knowing

- **`err` shadowing** with `:=` in a nested scope silently hides the
  outer error. `go vet`/linters catch some cases.
- **Slices share backing arrays** — `append` can mutate a slice you
  thought was independent; `copy` when you need isolation.
- **`defer` in a loop** stacks up until the function returns (not the
  loop iteration) — can exhaust resources.
- **Unchecked errors** — Go makes you handle errors explicitly; ignoring
  them with `_` hides failures. Linters flag it.
- **`nil` map writes panic** — reading a nil map is fine; writing panics.
  Initialize with `make`.

## Anti-Patterns

### Forgetting `go mod tidy`

A `go.mod`/`go.sum` with unused or missing deps. Run `tidy` before
pushing; it's an expected hygiene step.

### Not running `-race`

Shipping concurrent code never tested under the race detector. Run
`go test -race` in CI; races are otherwise nearly invisible.

### Returning typed nil as error

The nil-interface trap producing phantom non-nil errors. Return literal
`nil`.

### Ignoring errors with `_`

Discarding errors to make code compile. Handle them; that's the Go way.

### Leaking goroutines

Spawning goroutines with no termination path. Always provide a way out
(context, done channel).

## See Also

- [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)
- [../14-advanced/working-in-distributed-systems.md](../14-advanced/working-in-distributed-systems.md)
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)
- [rust.md](rust.md)
