# Entry Points

Every codebase has three to five **entry points** — places where the
graph of code touches the outside world. Find these and the rest is a
graph you can walk.

## The Three Entry Points

### 1. The Process Entry Point

Where does execution *start*?

| Language / framework | Typical location |
|---|---|
| Go | `cmd/*/main.go` or `main.go` at root |
| Rust binary | `src/main.rs` |
| Rust library | `src/lib.rs` (no main; entry is the public API) |
| Node.js | `index.js` / `index.ts`, or the `main` field of `package.json` |
| Python CLI | `__main__.py`, or the `[project.scripts]` entry in pyproject |
| Java | class with `main` method; sometimes auto-discovered Spring boot class |
| C/C++ | `main.c`, `main.cpp` |
| Ruby | `bin/<command>`, `exe/<command>` |
| Web framework (Rails, Django, Spring) | the framework hides `main`; entry is the route table |

If unsure, check:

```bash
# Common patterns
find . -maxdepth 3 -name 'main.*' -not -path '*/test*' -not -path '*/node_modules*'
grep -l 'fn main\|func main\|def main\|public static void main' -r --include='*.{go,rs,py,java,cpp,c}'

# For Node:
jq .main package.json
jq .scripts package.json

# For Python:
grep -A5 'project.scripts\|entry_points' pyproject.toml setup.py setup.cfg 2>/dev/null
```

### 2. The Test Entry Points

Where does the test runner start?

| Language | Config to read |
|---|---|
| Go | `go test ./...` — tests live next to source in `*_test.go` |
| Rust | `cargo test` — tests in `tests/`, `src/`, or `#[cfg(test)] mod tests` |
| Python | `pytest.ini`, `pyproject.toml#tool.pytest`, `tox.ini` |
| JavaScript | `jest.config.*`, `vitest.config.*`, `package.json#scripts.test` |
| Java | `pom.xml`, `build.gradle`, `src/test/` |
| Ruby | `spec_helper.rb`, `Rakefile` |

Don't just find them — *run* them. A test entry you can't invoke is a
test entry that won't help you.

### 3. The Build Entry Point

What produces the ship-able artifact?

| Build system | Entry file |
|---|---|
| Make | `Makefile` |
| just | `justfile` |
| npm/pnpm/yarn | `package.json#scripts` |
| Cargo | `Cargo.toml` |
| Go | `go.mod` + go tool |
| Maven | `pom.xml` |
| Gradle | `build.gradle(.kts)` |
| Bazel | `BUILD.bazel`, `WORKSPACE` |
| Buck2 | `BUCK` |
| Nix | `flake.nix`, `default.nix` |
| Python | `pyproject.toml`, sometimes `setup.py` |
| CMake | `CMakeLists.txt` |

The build system tells you **what is actually shipped**. Reading the
build config often reveals architecture: which subprojects exist, what
gets vendored, what generation happens.

## Framework Entry Points

For service code, the "entry" is often the **route table** or **schema**:

### HTTP servers

| Framework | Routes typically in |
|---|---|
| Express (Node) | `app.use(...)`, `app.get(...)` — find the `app` variable |
| Fastify | Same idea, often via plugins |
| Django | `urls.py` (project + per-app) |
| Flask | decorators on functions; or `app.add_url_rule(...)` |
| FastAPI | decorators; OpenAPI schema visible at `/docs` |
| Rails | `config/routes.rb` |
| Spring | `@RestController` classes; or `@RequestMapping` |
| Go (chi, gorilla, gin, echo) | the route setup function |
| Go stdlib | `http.HandleFunc(...)` |
| Axum, Actix (Rust) | `Router::new().route(...)` etc. |

### gRPC / RPC services

Look for `.proto` files. Each `rpc` is an entry point. Generated server
stubs are the bridge into code.

### Message handlers / queue consumers

Search for handler registrations:

```bash
rg 'handle_message|on_message|@consumer|subscribe' --type-add 'src:*.{go,rs,py,js,ts,java}' -t src
```

Background workers often have their own entry: `worker.go`, a Celery
`@task` decorator, etc.

### CLIs (sub-command frameworks)

| Framework | Look for |
|---|---|
| Cobra (Go) | `cobra.Command{Use: "..."}` |
| Clap (Rust) | `Command::new(...)` or `#[derive(Parser)]` structs |
| Click / Typer (Python) | `@click.command()`, `@app.command()` |
| Commander (Node) | `.command('...')` chains |
| Yargs (Node) | `.command(...)` |

The command tree is the entry-point tree.

## Why Entry Points Matter

Once you've identified the entries:

- **Top-down reading becomes natural.** You start where execution starts.
- **You map the public surface.** What's accessible from outside is
  exactly what's reachable from an entry.
- **Dead-code candidates emerge.** Anything not reachable from any entry
  is suspect.
- **Tests have a known shape.** Each test exercises a path from an entry
  or from internal code.

## Practical Exercise: Map All Entries

In a new repo, spend 15 minutes producing a list like:

```
ENTRIES
- cmd/server/main.go     → HTTP server, port 8080
- cmd/migrate/main.go    → DB migration CLI
- cmd/worker/main.go     → background queue worker
- internal/api/v1/       → HTTP routes (mounted under /v1)
- internal/jobs/         → worker handlers (registered in cmd/worker)
- proto/service.proto    → gRPC schema
- scripts/seed.py        → one-off data seed
```

This list, kept in your scratchpad, is more useful than a 1000-line
architecture diagram.

## Finding Entries in Unfamiliar Code

When you can't find an entry:

1. **Look at the build output.** What binaries does CI publish?
2. **Look at the README's quick-start.** Whatever the user runs first.
3. **Look at the Dockerfile.** `CMD` or `ENTRYPOINT` is the entry.
4. **Look at systemd / k8s manifests.** They invoke the entry directly.
5. **Read the CI workflows.** They build and exercise entries.

When none of those help, you're probably in a library, not an app. Then
the entry is the public API — look at the `lib.rs`, `__init__.py`,
`index.ts`.

## See Also

- [following-data-flow.md](following-data-flow.md) — what to do after finding the entry
- [../01-orientation/mental-model.md](../01-orientation/mental-model.md) — placing entries in your model
- [../03-reading-code/reading-strategies.md](../03-reading-code/reading-strategies.md) — top-down from entries
