# Observability First

Before guessing or reading code, **look**. Logs, traces, metrics, stack
traces — these are evidence the system is offering you. Use it.

## The Hierarchy

When investigating, in order:

1. **Read the error.** The full error, not just the headline.
2. **Read the stack trace.** All of it, bottom to top.
3. **Read the logs.** Just before and after the failure.
4. **Check metrics.** Did something spike, drop, or change shape?
5. **Look at traces.** For distributed systems: which span failed?
6. **Profile / instrument.** For perf or memory: actual measurements.
7. **Then read code.** Now you have evidence, not theories.

Most engineers jump to step 7. Steps 1–6 are 10x faster.

## Reading Error Messages

Errors have layers. Read all of them.

### Wrapped errors

```
failed to process order: failed to reserve inventory: out of stock: item="widget-123"
```

Four layers. The deepest (rightmost) is usually the root cause. The
others are context.

When you see `: ` chains in an error message, treat the rightmost piece
as the lead.

### Error types vs error messages

Modern error systems carry typed errors:

```go
if errors.Is(err, ErrOutOfStock) {
    // handle out-of-stock specifically
}
```

When debugging, the **type** is often more diagnostic than the message.
The message may be cosmetic; the type tells you where it came from.

### Where the error originated

Stack traces (next section) tell you where. Some error frameworks
(Rust's `anyhow::Error`, Go's wrapped errors with `%w`) carry the
origin SHA / line.

## Reading Stack Traces

A stack trace looks intimidating but is just a sequence of frames.

```
panic: nil pointer dereference

goroutine 1 [running]:
main.processOrder(0x0)
    /app/orders/process.go:42
main.handleOrderRequest(0xc00001a0e0, 0xc000010100)
    /app/handlers/orders.go:31
net/http.HandlerFunc.ServeHTTP(...)
    /usr/local/go/src/net/http/server.go:2069
```

Read **bottom-up** for the request path:

1. HTTP request came in.
2. Handler at `orders.go:31` called `processOrder`.
3. `processOrder` at `process.go:42` dereferenced a nil pointer.

The bug is at the **top** of the trace (line 42), but understanding
*how it got there* requires reading the bottom-up path.

### Filtering noise

Most stack traces include framework frames (`net/http.HandlerFunc...`).
Skip those. Focus on:

- Frames from your code.
- The exact failing line.

### Cross-language considerations

| Language | Trace style |
|---|---|
| Python | Most recent call last (bottom is failure) |
| Java | Causes chained with "Caused by:" |
| Go | Goroutine-aware, panic shows multiple |
| Rust | Backtrace if `RUST_BACKTRACE=1` set |
| Node.js | Async traces fragmented; use `--async-stack-traces` |

Async stack traces are notoriously bad in some languages. If a Node
trace stops at "anonymous", enable async traces and re-run.

## Logging Strategy While Debugging

Add temporary logging when traces don't show what you need:

```python
import logging
log = logging.getLogger(__name__)

def process_order(o):
    log.debug("entering process_order: order=%r", o)
    result = inner(o)
    log.debug("inner returned: result=%r", result)
    return result
```

Don't use `print` in production code. Use the project's logging
framework.

### Verbosity levels

Most loggers have levels:
- `DEBUG` — for development.
- `INFO` — operational events.
- `WARN` — unusual but not failing.
- `ERROR` — failures.

Default is often `INFO`. When debugging, crank to `DEBUG`:

```bash
LOG_LEVEL=debug ./app
```

Check the project's docs for how to enable.

### Avoid log spam in PRs

Temporary logging should be **removed** before commit. Use linters
(`no-console`, `no-print`, etc.) to enforce.

## Reading Metrics

For services with metrics (Prometheus, Datadog, etc.):

- **Request rate** — is traffic normal?
- **Error rate** — spike?
- **Latency** — p50, p95, p99?
- **Resource use** — CPU, memory, file descriptors?

Useful queries:
- "Show p99 latency on this endpoint over the last 24h."
- "Show error rate by error type."
- "Compare this hour to the same hour last week."

Often a metric shape reveals the cause: "p99 spiked at 14:00" + "deploy
happened at 14:00" = strong lead.

## Reading Traces (Distributed)

For systems with distributed tracing (OpenTelemetry, Jaeger, Zipkin):

- Find the trace for one failed request.
- Look at the span tree — which service, which method?
- Look at span durations — where did time go?
- Look at span errors — which one threw?

A single trace often answers "where in the system did this go wrong?"
faster than hours of log-reading.

## The `curl -v` Trick

For HTTP-level issues, `curl -v` shows the entire request and response:

```bash
curl -v https://api.example.com/orders -X POST \
    -H 'Content-Type: application/json' \
    -d '{"id": 1}'
```

You'll see:
- DNS resolution.
- TLS handshake.
- Request headers and body.
- Response headers and body.

Often diagnostic for "the server is doing what?" or "why is my header
not what I think?"

## `tcpdump` / Wireshark

For network-level mysteries:

```bash
sudo tcpdump -i any -n -A 'port 8080'
```

Shows raw packets. Excellent for protocol-level bugs.

## OS-Level Tools

| Tool | Use for |
|---|---|
| `strace` | Linux syscalls per process |
| `dtrace` / `dtruss` | macOS/BSD/Solaris syscalls |
| `lsof` | Open files, sockets, ports |
| `ps`, `top`, `htop` | Process state |
| `ss`, `netstat` | Network sockets |
| `iostat` | Disk I/O |

Example: "why is my app hanging?"

```bash
# Find PID
ps aux | grep myapp

# See what it's doing
strace -p <pid>

# Or syscall summary
strace -p <pid> -c
```

Reveals if it's stuck on I/O, in a busy loop, or doing nothing.

## Memory and Heap

For memory bugs:

| Language | Tool |
|---|---|
| Go | `pprof` (heap, allocs, goroutines) |
| Rust | `dhat`, `heaptrack` |
| Python | `tracemalloc`, `objgraph` |
| Node | `--inspect`, heap snapshots in Chrome DevTools |
| Java | jmap, MAT, jconsole |
| C/C++ | Valgrind, ASan, heaptrack |

For "memory leak" hunts, take snapshots at different times and compare.

## CPU Profiling

For "why is this slow":

```bash
# Go
go tool pprof http://localhost:6060/debug/pprof/profile

# Python
py-spy top --pid <pid>
py-spy record -o profile.svg --pid <pid>

# Node
clinic flame -- node app.js

# Linux generic
perf record -F 99 -p <pid> -g
```

The output: flame graphs, top functions, or interactive UIs. A flame
graph in 5 minutes saves hours of "what's slow?" theorizing.

## Production vs Dev

Some bugs only manifest in production. Bring production data to dev
when possible (anonymized):

- Real user input shapes (the long-tail of edge cases).
- Production scale (some bugs are concurrency or data-volume specific).

But don't develop with real PII. Anonymize first.

## When Observability Is Missing

Many bugs are unfindable in projects with poor observability. If you're
debugging and there are no logs, no metrics, no traces — that's a
finding. Either:

- Add the missing observability (PR opportunity).
- Improve existing observability incrementally.

Better tooling for future debuggers is a strong contribution.

## See Also

- [minimal-reproduction.md](minimal-reproduction.md) — combining repro with observation
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md) — deeper into the toolkit
- [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)
