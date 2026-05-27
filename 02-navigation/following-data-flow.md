# Following Data Flow

Code is structure. Behavior is flow. To debug or extend a system, you
often need to trace **data flowing through code** — request to response,
input to output, event to side effect.

## Two Kinds of Flow

### Control flow

What code runs next? Branches, loops, function calls. Static, mostly
visible by reading.

### Data flow

What values move through the system? Often invisible just by reading —
the same function transforms many different data shapes.

You need both. Reading code that only shows control flow misses bugs
that live in data shapes (wrong field, wrong unit, wrong nullability).

## The Trace-One-Request Walk

For services, do this exactly once per unfamiliar codebase:

1. **Pick an endpoint** — preferably a small one (`GET /users/:id`, not a
   complex search endpoint).
2. **Find the route.** Use [entry-points.md](entry-points.md).
3. **Open the handler.** Read the signature; note the input type.
4. **Step into the next call.** Note what arguments it gets.
5. **Continue until you hit I/O** — database, external API, queue.
6. **Walk back up**, noting how the response is shaped.

Take notes. After 30 minutes, you'll have a transcript like:

```
GET /users/:id
  → router.go: matched to users.GetByID
    → handlers/users.go:GetByID(ctx, w, r)
      → users.GetByID receives params.id (string)
      → calls service.UserService.Get(ctx, userID)
        → service/users.go: type UserService
        → calls repo.Users.Get(ctx, userID)
          → storage/users.go: SQL query
          → returns *model.User or error
      → service returns presenter.UserView
    → handler json-encodes UserView; writes 200
```

This 30-minute exercise teaches you the project's vocabulary, layering,
error model, and serialization conventions — all at once.

## Data Shapes Are Key

When tracing, note **the shape changes at each layer**:

- HTTP layer: JSON / form-encoded
- Application layer: domain types (e.g., `User` struct)
- Persistence layer: rows / records
- External calls: protocol buffers, JSON for upstream APIs

Most bugs live at these conversions. "Field is null in the response" often
means a conversion dropped it. Trace it through.

## Tools for Tracing

### LSP

Go-to-definition and find-references are your primary tools. Walk the
call graph step by step.

### Stack traces

If you can trigger an error, the stack trace is a free trace of one
execution path:

```
panic: nil dereference
    at storage/users.go:42 in repo.Users.Get
    at service/users.go:18 in service.UserService.Get
    at handlers/users.go:31 in handlers.GetByID
    at net/http
```

Read **bottom-up** for the request path. Each frame is a layer.

### Debugger

The fastest way to understand flow is to *step* through it:

| Language | Debugger | Tip |
|---|---|---|
| Go | `dlv debug`, `dlv test` | Use VS Code/Goland; raw CLI is harder |
| Rust | `lldb`, `gdb`, `rust-gdb` | `cargo test -- --test-threads=1` for clarity |
| Python | `pdb`, `ipdb`, `breakpoint()` | `breakpoint()` is built-in since 3.7 |
| Node/TS | `node --inspect`, VS Code debugger | source maps essential |
| Java/Kotlin | JetBrains' debugger | Best-in-class |
| C/C++ | `gdb`, `lldb` | RR for time-travel (Linux) |
| Ruby | `byebug`, `debug` | `binding.break` in modern Ruby |

The minimal debugger workflow:
1. Set a breakpoint at the entry of interest.
2. Trigger the path.
3. Step into each call; observe variables.

Far faster than reading.

### Logs (added temporarily)

When debugger setup is heavy:

```python
# Python
import logging; logging.basicConfig(level=logging.DEBUG)
# or just:
print(f"DEBUG: arrived at {locals()=}", flush=True)
```

```go
// Go
log.Printf("DEBUG: at handler, req=%+v", req)
```

Remove these before committing (use a linter to enforce).

### Tracing tools

In production, instrumentation (OpenTelemetry, Jaeger, Zipkin) gives you
flow across services. Trace IDs in logs let you correlate.

If the project has tracing, *use it locally too*. Spin up Jaeger via
docker, point the app at it, watch flow visually.

### `strace` / `dtrace` / `lsof`

For "what is this process actually doing at the OS level":

```bash
strace -p <pid>                     # all syscalls
strace -e openat -p <pid>           # only file opens
strace -e network -p <pid>          # only network syscalls
lsof -p <pid>                       # what files/sockets are open
```

Excellent for "why is my app slow / blocking / making weird syscalls."

## Following Data Across Process Boundaries

Microservices, queues, and external APIs break direct trace. Strategies:

### Correlation IDs

Most production systems propagate a request ID via headers (`X-Request-Id`,
`traceparent`). If you're investigating one user's request, finding all
log lines with that ID gives you the cross-service trace.

### Schema-first thinking

For RPC: read the `.proto` / IDL. Each method is a hop.

For events: read the schema (Avro, JSON Schema, etc.). Each event type
is a flow node.

### Sequence diagrams

For a complex flow you'll revisit, sketch a sequence diagram. ASCII works:

```
Client          API           OrderSvc       Inventory
  |              |                |              |
  |--POST /buy-->|                |              |
  |              |--Place(o)----->|              |
  |              |                |--Reserve()-->|
  |              |                |<--OK---------|
  |              |<--OrderOK------|              |
  |<--201--------|                |              |
```

Tools like mermaid or plantuml render this if you want it shareable.

## Pitfalls When Tracing

### Reflection / dynamic dispatch

Code like `handler := handlers[name](req)` loses LSP. Find the map
construction site:

```bash
rg 'handlers\[' --type go
rg 'handlers\s*=\s*{' --type python
```

…and read the registration site to find candidates.

### Plugins / extension points

If the framework loads plugins, the flow goes through the plugin loader.
Read the loader; understand what plugins exist and in what order.

### Async / callbacks / promises

Where execution "continues" is not the next line. For:
- **Node.js promises**: trace `.then(...)` chains.
- **Go goroutines**: where is the goroutine started? It runs concurrently.
- **Python asyncio**: `await` points are yield points.
- **Rust async**: `.await` is a suspend point; data may not arrive in order.

Add temporary logging with timestamps to see the actual order.

### Background timers / cron / scheduled

Some flows aren't triggered by users at all. Search for `setInterval`,
`time.Tick`, `@Scheduled`, `cron.` to find them.

## Building Your Own Flow Map

After a few traces, build a project-level flow map for your scratchpad:

```
== Major flows ==
User signup:
  POST /signup
    → users.Signup → emailer.SendWelcome (async via queue)
                  → service.CreateUser   (writes DB, returns *User)
    → emit "user.created" event           (consumed by analytics, billing)

Order placement:
  POST /orders
    → orders.Place
      → inventory.Reserve (RPC)
      → payment.Charge (RPC)
      → orders.Save (DB)
    → emit "order.placed"

Inventory restock (background):
  cron every 5min: inventory.Replenish
    → calls supplier API
    → updates stock
    → emits "inventory.updated"
```

This map is the highest-leverage artifact in your scratchpad.

## See Also

- [entry-points.md](entry-points.md) — where flows start
- [../03-reading-code/](../03-reading-code/) — reading techniques
- [../14-advanced/working-in-distributed-systems.md](../14-advanced/working-in-distributed-systems.md) — flows across services
