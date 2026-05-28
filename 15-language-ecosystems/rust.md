# Rust

Rust's tooling is widely considered the gold standard — `cargo` does
nearly everything, consistently. The cost is a steep learning curve: the
borrow checker, lifetimes, and a split async ecosystem are where
newcomers struggle.

## cargo: The One Tool

`cargo` is build system, package manager, test runner, and more. `Cargo.toml`
declares the project; `Cargo.lock` pins exact versions.

```bash
cargo build                 # debug build
cargo build --release       # optimized build (much slower compile, fast run)
cargo run                   # build + run
cargo test                  # run tests (unit, integration, doc tests)
cargo check                 # type-check without producing a binary (FAST)
cargo doc --open            # build and view docs
cargo add serde             # add a dependency
cargo tree                  # dependency tree
cargo tree -i somecrate     # inverse: who depends on this? (see right-repo-problem)
```

**`cargo check`** is your friend during development — it's far faster
than a full build because it skips codegen. Use it for the inner loop;
build only when you need to run. Compile times are Rust's main ergonomic
complaint.

## rustup: Toolchain Management

`rustup` manages Rust versions and components:

```bash
rustup show                 # active toolchain (respects rust-toolchain.toml)
rustup update
rustup component add clippy rustfmt rust-analyzer
```

Projects pin a toolchain via `rust-toolchain.toml`; `rustup` honors it
automatically. **stable** vs **nightly** matters — some crates/features
need nightly; the project's pin tells you.

## Formatting & Linting

```bash
cargo fmt                   # rustfmt — the one true format (like gofmt)
cargo clippy                # the linter — exceptionally good
cargo clippy --fix          # autofix what it can
cargo clippy -- -D warnings # treat lints as errors (common in CI)
```

**Clippy** is one of Rust's best tools — its lints teach you idiomatic
Rust as you go. CI often runs `clippy -D warnings` (deny warnings);
mirror that locally. `rustfmt` ends formatting debate.

## rust-analyzer

The LSP, and it's excellent — go-to-def, inlay hints (especially
valuable for inferred types and lifetimes), real-time errors, quick
fixes. Essential for navigating Rust (see
[../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)).
Inlay type hints are particularly worth enabling — Rust infers a lot, and
seeing the inferred types is a huge comprehension aid.

## Useful Extras

```bash
cargo expand                # show code after macro expansion (demystifies macros)
cargo flamegraph            # profiling flame graph (see performance-investigation)
cargo nextest run           # faster, nicer test runner
cargo bench / criterion     # benchmarking
cargo deny / cargo audit    # license + security auditing of deps (see license-math)
```

`cargo expand` is invaluable when a macro's behavior is opaque — it shows
you the generated code.

## Classic Gotchas (a.k.a. "Fighting the Borrow Checker")

### Ownership and borrowing

The defining Rust learning curve. The rules: each value has one owner;
you can have *either* one mutable borrow *or* any number of immutable
borrows, never both at once. This prevents data races and
use-after-free at compile time — but it rejects patterns that are fine in
other languages.

- The compiler is (almost always) right. "Fighting the borrow checker"
  usually means your *design* has an aliasing/lifetime problem the
  checker is surfacing.
- Don't reach for `clone()` everywhere to silence it — sometimes correct,
  often a smell. Understand *why* it complains.
- Don't reach for `unsafe` to escape it — that's how you get the bugs
  Rust prevents.

### Lifetimes

Lifetime annotations (`'a`) tell the compiler how long references are
valid. Newcomers find them baffling; the key reframe: **lifetimes
describe relationships that already exist**, they don't *create*
behavior. You're documenting "this reference can't outlive that data,"
which the compiler then enforces. Most code needs few explicit
lifetimes (elision handles the common cases).

### The async ecosystem split

Rust's async is powerful but **fragmented**: async code generally needs a
*runtime*, and there are competing ones — **Tokio** (dominant), plus
others (async-std historically, smol, etc.). Consequences:

- Libraries are often tied to a specific runtime (usually Tokio).
- Mixing runtimes, or using a Tokio-dependent crate without a Tokio
  runtime, fails confusingly.
- "Function coloring" — `async fn` and sync fn don't mix freely; async
  propagates up the call stack.

Check which runtime the project uses (almost always Tokio) and stay
within it.

### Other gotchas

- **`String` vs `&str`** (owned vs borrowed string) and `Vec<T>` vs
  `&[T]` — the owned/borrowed distinction is everywhere; learn it early.
- **`Result` and `?`** — errors are values; the `?` operator propagates
  them. Forgetting to handle/propagate is a compile error (a feature).
- **`unwrap()` panics** — fine for prototypes and tests, a landmine in
  production. Prefer proper error handling; reviewers flag stray
  `unwrap`.
- **Move semantics** — using a value after it's moved is a compile error;
  surprising if you expect copy semantics. `Copy` types (integers, etc.)
  are the exception.
- **Trait coherence / orphan rule** — you can't `impl` a foreign trait on
  a foreign type; surprising when wrapping external types (the newtype
  pattern is the workaround).

## Anti-Patterns

### `clone()`-spamming to appease the borrow checker

Cloning everywhere to avoid understanding ownership. Sometimes right,
often a sign you haven't grasped the actual data flow. Understand the
complaint.

### Reaching for `unsafe` to escape the checker

Using `unsafe` to silence borrow errors reintroduces exactly the bugs
Rust prevents. Almost never the right fix for application code.

### `unwrap()` everywhere in production

Panic-on-error scattered through real code. Handle errors with `?` and
proper types; reserve `unwrap` for tests/prototypes.

### Full `build` in the inner loop

Waiting on full optimized builds when `cargo check` would answer "does it
compile?" in a fraction of the time. Check fast, build when you must run.

### Ignoring clippy

Skipping the linter that's actively teaching you idiomatic Rust. Run
clippy; read its suggestions.

## See Also

- [go.md](go.md)
- [cpp.md](cpp.md)
- [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)
- [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)
- [../13-hidden-knowledge/license-math.md](../13-hidden-knowledge/license-math.md)
