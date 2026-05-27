# Building a Mental Model

After 30 minutes of recon, you've absorbed surface facts. The next step
is building the **mental model** — the internal map of how the system
works that makes future code reading fast.

## What a Good Mental Model Contains

For most server/library projects, you want answers to:

1. **What are the layers?** (HTTP → service → repo → DB, etc.)
2. **Where does I/O cross the boundary?** (DB calls, HTTP clients, file
   system, external APIs.)
3. **Where does state live?** (Database, in-memory caches, files, queues.)
4. **What's the lifecycle?** (Request → response, job → completion,
   message → consumption.)
5. **What's the public API surface?** (CLI commands, HTTP endpoints,
   exported functions.)
6. **Where do plugins/extensions hook in?** (Middleware, observers,
   strategy interfaces.)
7. **What's the testing strategy?** (Unit, integration, e2e — and how
   are they organized.)

If you can sketch this on a napkin after a day of reading, you're
oriented.

## The "Trace One Request" Exercise

The single most efficient orientation exercise for service-style code:

> Pick one user-facing operation. Trace it from input to output, end to end.

For an HTTP service:

1. Find the endpoint definition (route → handler).
2. Walk into the handler.
3. Follow the call into business logic.
4. Follow that into data access.
5. Follow that to the database / external service.
6. Walk back: response shaping, serialization, error handling.

Use your LSP. Note every layer name. Note the *concepts* the project
uses ("controller," "service," "repository," "gateway," whatever they
call it).

After this one walk-through, you understand 70% of the layering.

## The "Trace One Test" Exercise

For library code (no user request):

1. Pick a representative test.
2. Read it: what's the input, what's the assertion?
3. Step *into* the function being tested (in the debugger or by reading).
4. Walk the same path the test takes.

Tests are often the cleanest entry points — they exercise one feature
with minimal setup.

## Identify the Glue

Most non-trivial projects have a "core" of business logic, surrounded by
"glue":

- **Adapters / drivers** — connecting to external services.
- **Configuration loading** — usually one or two files.
- **Logging, metrics, tracing** — cross-cutting.
- **Authn / authz** — middleware or decorator pattern.
- **Serialization** — JSON / protobuf / Avro layer.
- **Background jobs / queues** — separate from the request path.

Glue is repetitive but important to recognize. Once you know "all DB
access goes through `internal/storage`," you know where to look for any
DB question.

## Mental Model Patterns by Project Type

### CRUD service

Standard layering: HTTP handlers → services → repositories → DB.
- Look in `handlers/` / `controllers/` / `api/`.
- The service layer has business rules.
- The repository layer has DB queries.
- Errors propagate up; HTTP status mapping happens at the top.

### Library

- Public API at the top of one file (often `lib.rs`, `mod.go`, `__init__.py`).
- Internal modules below.
- Tests demonstrate intended usage.
- No I/O in the core; I/O lives in adapters or callers.

### CLI tool

- An `argv` parser at the top (cobra, clap, argparse).
- Each subcommand maps to a function.
- Often a thin layer over a library — find the library underneath.

### Compiler / interpreter / parser

- Lex → parse → analyze → optimize → emit pipeline.
- Each phase usually has its own module.
- AST / IR types are the most important data structures.

### Distributed system / orchestrator

- Many processes, communicating.
- Look for the **message types** and **RPC schemas** before reading code.
- State machines often hide in seemingly-imperative code.

### Game / simulation

- Update loop is the central abstraction.
- Per-frame logic vs per-event logic.
- Look for the "tick" function and walk outward.

### ML / data pipeline

- DAG of transformations.
- Find the orchestrator (DAG runner, Airflow, etc.).
- Data shapes (schemas) are more important than code.

## The Sketch

Once you've traced a request and identified the layers, *draw the model*.
This can be:

- A napkin sketch.
- A text file in your scratchpad.
- A whiteboard photo.

Sample (a hypothetical web service):

```
[HTTP] → middleware (auth, logging, rate-limit)
       → router → handler (in api/v1/...)
       → service (in services/...)
       → repository (in storage/...)
       → DB (Postgres) | external API (in clients/...)

separate paths:
- background jobs: queue (Redis) → worker (in workers/...)
- metrics: middleware → Prometheus
- logs: zap → stdout (collected by k8s)
```

A sketch this rough teaches you more than a 50-page architecture document
written by someone else, because *you made it.*

## When the Model is Wrong

You'll discover, often, that your initial model is incorrect:

- The "service" layer turns out to be empty pass-through.
- The "repository" actually has business logic.
- The "background worker" is called inline in some paths.

This is normal. Update your sketch. Note the surprises — they're
candidates for either refactoring or for "weird but intentional, here's
why" learning. See [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md).

## Common Mental Model Failures

- **Assuming the project is what its README claims.** Real systems
  accumulate divergence.
- **Skipping the I/O boundary.** People over-focus on logic and miss
  the network/disk where bugs live.
- **Ignoring background paths.** Cron jobs, queues, webhooks — these
  are often where weird behaviors live.
- **Treating the model as static.** Update it as you learn.

## Mental Model and Contribution

Your first few PRs should *stay inside one layer of your model*. Don't
do "while I'm here, refactor this cross-layer thing." You don't yet know
why the layers are shaped that way.

After 5–10 PRs, your model is solid enough to suggest layer changes
themselves — but as proposals, not surprise PRs.

## See Also

- [../02-navigation/following-data-flow.md](../02-navigation/following-data-flow.md) — the next level of detail
- [../03-reading-code/](../03-reading-code/) — techniques to refine your model
- [../14-advanced/working-with-legacy-code.md](../14-advanced/working-with-legacy-code.md) — for projects where the model is "complicated"
