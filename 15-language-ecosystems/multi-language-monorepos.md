# Multi-Language Monorepos

A polyglot monorepo holds several languages — say a Go backend, a
TypeScript frontend, Python data jobs, and some Rust — in one repo. The
challenge is gluing heterogeneous per-language toolchains into one
coherent build, test, and ownership model. Read
[../14-advanced/working-in-monorepos.md](../14-advanced/working-in-monorepos.md)
first; this is the cross-language layer on top.

## Why They Exist (and Why They're Hard)

The same monorepo benefits — atomic cross-cutting changes, shared
tooling, one source of truth — apply, but multiplied difficulty:

- Each language has its *own* package manager, build tool, and test
  runner (the whole of this section's other pages).
- A naive setup forces every developer to install *every* language's
  toolchain to build *anything*.
- "Run the affected tests" must span language boundaries.

So polyglot monorepos lean on a **language-agnostic build system** to
unify the chaos.

## Polyglot Build Systems

The tools designed for heterogeneous monorepos:

| Tool | Origin | Model |
|---|---|---|
| **Bazel** | Google | Hermetic, sandboxed, content-addressed; rules per language |
| **Buck2** | Meta | Similar to Bazel; fast, Rust-based |
| **Pants** | — | Dependency inference (less manual BUILD wiring); Python-strong |
| **Nx** | — | JS-rooted but extensible to other languages via plugins |
| **Please** | — | Bazel-like, lighter |

The shared idea: describe each unit of code and its dependencies in a
build file (`BUILD`/`BUILD.bazel`), and the tool builds a **graph across
all languages**. A change's affected targets — in any language — are
computed from that graph, so CI and local builds run only what's
impacted, regardless of language.

```bash
bazel build //backend/...           # build a subtree (any language)
bazel test //frontend:unit          # a specific target
bazel query 'rdeps(//..., //lib:core)'   # what depends on this? (cross-language)
```

## Hermeticity and Toolchain Sandboxing

The feature that makes polyglot builds sane: **hermetic builds**. Bazel
and Buck2 sandbox each build action with *pinned, declared* toolchains
and inputs — the compiler version, the dependencies, everything — so:

- A build doesn't depend on what *you* happen to have installed.
- The Go build uses the repo's pinned Go; the Rust build its pinned
  Rust; you don't manually install either.
- Builds are reproducible and cacheable (often shared via a **remote
  cache** so the whole team/CI reuses each other's build results).

This solves the "install every toolchain" problem: the build system
provisions them, sandboxed, per the repo's pins. The cost is upfront
complexity — Bazel/Buck have a real learning curve and require writing
BUILD files (or generating them).

## When *Not* to Use a Heavy Build System

A polyglot repo doesn't always need Bazel. Lighter approaches:

- **Per-language tooling + a task runner** (Make, `just`, Taskfile) that
  delegates to each language's native tools. Simpler; less unified
  caching/affected-detection.
- **Nx or Turborepo** if the repo is JS-dominant with some other
  languages at the edges.
- **Independent projects with a top-level orchestrator** when the
  languages barely interact.

Match the tool to the coupling: tightly-coupled cross-language builds
justify Bazel; loosely-coupled ones often don't. Adopting Bazel for a
small repo is over-engineering.

## Ownership: CODEOWNERS by Path

In a polyglot repo, ownership maps cleanly to paths/languages:

```
# CODEOWNERS
/backend/      @backend-team
/frontend/     @web-team
/data/         @data-eng
/libs/shared/  @platform-team
*.proto        @api-team        # the cross-language contract
```

- Each language area has its owning team and reviewers.
- The **interface files** (Protobuf/gRPC `.proto`, OpenAPI specs,
  Thrift, JSON schemas) often get their *own* owners — they're the
  cross-language contracts, and changing them ripples into every
  language that consumes them. Treat them as high-stakes.

See [../14-advanced/working-in-monorepos.md](../14-advanced/working-in-monorepos.md)
for the CODEOWNERS mechanics.

## Cross-Language Boundaries: Schemas as Contracts

Languages in a monorepo usually talk via a **schema/IDL** rather than
direct calls:

- **Protocol Buffers / gRPC**, **Thrift**, **FlatBuffers** — define a
  schema once; generate typed bindings for each language.
- **OpenAPI / JSON Schema** — for HTTP/REST boundaries.

The build system generates per-language code from these schemas as part
of the build. The implication for you: **a change to a `.proto` is a
cross-language change** — it regenerates code in Go, TS, Python, etc., and
can break any of them. Use the build graph to find every consumer before
changing a schema, and roll schema changes out compatibly (expand-
contract — see
[../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)).

## Cross-Language Refactors

Renaming or restructuring across languages is the hardest case:

- **No single LSP spans all languages**, so editor rename won't carry
  across the boundary. The schema/IDL is your pivot — rename there, regen,
  fix each language's generated-code consumers.
- **Use the build graph**, not just `grep`, to find consumers — text
  search misses generated code and cross-language usage (see
  [../02-navigation/search-tools.md](../02-navigation/search-tools.md)).
- **Per-language codemods** for the mechanical parts (`gofmt -r`,
  `jscodeshift`, `comby`/`ast-grep` for language-agnostic structural
  edits).
- **Stage it**: change the schema compatibly, migrate each language's
  consumers, then tighten — rather than one atomic mega-change, unless
  the build genuinely lets you do it atomically.

## Anti-Patterns

### Bazel for a tiny repo

Adopting a heavy hermetic build system for a repo that two native
toolchains and a Makefile would serve. Match the tool to the coupling.

### Requiring every toolchain installed manually

A polyglot repo where you must hand-install Go, Node, Python, and Rust to
build anything. Hermetic builds (or at least documented, pinned
toolchains) should provision them.

### grep-only cross-language refactors

Relying on text search across languages, missing generated code and
dynamic usage. Use the build graph and the schema as the pivot.

### Treating a schema change as local

Editing a `.proto` as if it only affects one service — it regenerates and
can break every language that consumes it. Find all consumers; roll out
compatibly.

### No interface ownership

Leaving cross-language contract files (`.proto`, OpenAPI) unowned, so
breaking changes slip through. Give the contracts explicit owners.

## See Also

- [../14-advanced/working-in-monorepos.md](../14-advanced/working-in-monorepos.md)
- [../02-navigation/search-tools.md](../02-navigation/search-tools.md)
- [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)
- [../13-hidden-knowledge/right-repo-problem.md](../13-hidden-knowledge/right-repo-problem.md)
- [cpp.md](cpp.md)
