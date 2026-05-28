# Working in Monorepos

A monorepo holds many projects — sometimes the whole company — in one
repository. The navigation, build, and ownership rules differ enough
from single-project repos that they deserve their own playbook.

## Why Monorepos Exist

The appeal: one place, atomic cross-project changes, shared tooling, no
"which version of the shared library?" hell.

- **Atomic changes** across many components in one commit/PR.
- **Single source of truth** for shared code and config.
- **Unified tooling** — one build system, one lint config, one CI.
- **Easy cross-project refactors** — rename an API and all its callers in
  one change.

The cost: scale problems. A naive `git clone`, `grep`, or "build
everything" doesn't work when the repo is enormous.

## Orientation Differs

Your first-contact routine (see [../01-orientation/](../01-orientation/))
adapts:

- **Find *your* project.** You rarely work on the whole thing. Locate
  the directory/package you own and treat it as your working unit.
- **Read the workspace config**, not just one manifest. It declares the
  projects and how they relate:

```bash
# Common monorepo manifests / workspace declarations
cat pnpm-workspace.yaml package.json   # JS (workspaces field)
cat nx.json turbo.json                 # Nx / Turborepo
cat WORKSPACE BUILD MODULE.bazel       # Bazel
cat go.work                            # Go workspaces
cat Cargo.toml                         # Rust [workspace] members
cat pants.toml                         # Pants
```

- **Map the project graph.** Which packages depend on which? The build
  tool can usually print this; it's your dependency map.

## Code Ownership Boundaries

In a large monorepo, *who reviews what* is formalized:

- **`CODEOWNERS`** maps paths to owning teams/people. Editing a path
  auto-requests its owners as reviewers. Read it to know who must
  approve your change.

```bash
cat CODEOWNERS .github/CODEOWNERS docs/CODEOWNERS
# path → owner; the most specific match wins
```

- **Respect the boundaries.** A change touching three teams' directories
  needs three approvals and is harder to land. Scope your change to as
  few ownership domains as possible — a monorepo version of scope
  discipline (see
  [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)).
- **Cross-cutting changes** (touching a shared library everyone uses)
  are high-stakes: you may break dozens of downstream projects. Use the
  build graph to find *all* affected consumers before you change a
  shared interface.

## Build Systems

Monorepos need build tools that understand the project graph and only
rebuild what changed. The major ones:

| Tool | Ecosystem | Model |
|---|---|---|
| **Bazel** | Polyglot (Google-origin) | Hermetic, content-addressed, heavy but scalable |
| **Buck2** | Polyglot (Meta-origin) | Similar to Bazel; fast |
| **Pants** | Polyglot (Python-strong) | Dependency inference, lighter setup |
| **Nx** | JS/TS-strong, extensible | Task graph, affected-detection, caching |
| **Turborepo** | JS/TS | Task pipeline + remote caching, lighter |

The shared idea: a **task/build graph** plus **caching**, so you build
and test only what your change *affects*, not the whole repo.

```bash
# "What does my change affect, and only run that"
nx affected --target=test
turbo run test --filter='...[origin/main]'
bazel test //my/project/...           # scoped to a target subtree
```

Learning your repo's "affected"/scoped commands is the single biggest
productivity unlock — it's the difference between a 30-second test run
and a 30-minute one.

## Partial / Sparse Checkouts

When the repo is too large to clone or work with whole:

```bash
# Don't download all file contents up front (blobless clone)
git clone --filter=blob:none <url>

# Only materialize the directories you need
git sparse-checkout init --cone
git sparse-checkout set apps/my-app libs/shared
```

- **Sparse checkout** materializes only the directories you care about.
- **Partial clone** (`--filter=blob:none`) fetches blobs on demand
  instead of the entire history's contents.
- Some shops use a **virtual filesystem** (Git's `scalar`, or VFS
  tooling) so the giant repo appears whole but fetches lazily.

## Scoped CI

CI in a monorepo must not run *everything* on every change — it tests
only what's affected:

- Your change to `apps/foo` triggers `foo`'s tests and the tests of
  anything that *depends on* `foo`, not the whole repo.
- This relies on the build graph being accurate. If you add a dependency
  the tool can't see, CI may *miss* a break.
- **Read the CI config** to know what your change will actually trigger,
  and run the same scoped commands locally before pushing (see
  [../11-tooling/local-ci.md](../11-tooling/local-ci.md)).

## Cross-Project Refactors

The monorepo superpower: change an API and *all* its callers atomically.

- **Use the build graph** to find every consumer of what you're
  changing — don't rely on `grep` alone, which misses dynamic usage.
- **Codemods** (`jscodeshift`, `gofmt -r`, `comby`, `ast-grep`,
  language-aware tools) apply mechanical changes across the whole repo
  consistently. See
  [../02-navigation/search-tools.md](../02-navigation/search-tools.md).
- **Separate mechanical from substantive** even here: a huge
  mechanical codemod in one commit, the substantive change in another,
  so reviewers can verify each (see
  [../13-hidden-knowledge/merge-strategies.md](../13-hidden-knowledge/merge-strategies.md)).
- **Stage wide changes** when atomic isn't required: expand-contract
  still applies even in a monorepo (see
  [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)).

## Navigation at Scale

Plain tools strain at monorepo scale:

- **`rg`** is still fast, but scope it (`rg foo apps/my-app`) to avoid
  searching millions of files.
- **The LSP** may need configuration to index only your project, not the
  whole tree (see [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)).
- **Code search tools** (Sourcegraph, the host's built-in search, or the
  build tool's query) are often necessary above a certain size.

## Anti-Patterns

### Building/testing the whole repo

Running the entire suite when your change affects one package. Learn the
"affected"/scoped commands; let the build graph do the work.

### Ignoring CODEOWNERS

Touching many teams' paths casually, then stalling on review across all
of them. Scope to the fewest ownership domains; expect more reviewers
when you don't.

### grep-only cross-project refactors

Relying on text search to find callers in a polyglot monorepo misses
dynamic and cross-language usage. Use the build graph for the
authoritative consumer list.

### Cloning the giant repo whole

Waiting an hour for a full clone when sparse/partial checkout would give
you your slice in seconds. Use the scale tooling.

### Unscoped searches

`rg foo` across ten million files when you meant your app. Scope
searches to your project directory.

## See Also

- [working-with-legacy-code.md](working-with-legacy-code.md)
- [../01-orientation/repo-files.md](../01-orientation/repo-files.md)
- [../02-navigation/search-tools.md](../02-navigation/search-tools.md)
- [../11-tooling/local-ci.md](../11-tooling/local-ci.md)
- [../15-language-ecosystems/multi-language-monorepos.md](../15-language-ecosystems/multi-language-monorepos.md)
