# Dependency Traversal

Sometimes the answer to your question isn't in *your* code — it's in a
dependency's code. Most developers treat dependencies as black boxes.
That's a mistake. Most dependencies are readable, and reading them is
often the fastest path to understanding.

## Where Dependencies Live Locally

| Ecosystem | Location |
|---|---|
| Node.js | `node_modules/<pkg>/` (npm/pnpm/yarn) |
| Python (uv, pip + venv) | `.venv/lib/python*/site-packages/<pkg>/` |
| Python (system) | `/usr/lib/python*/site-packages/<pkg>/` |
| Rust | `~/.cargo/registry/src/index.crates.io-*/<pkg>-<ver>/` |
| Go | `~/go/pkg/mod/<pkg>@<ver>/` (read-only!) |
| Ruby | `~/.gem/ruby/<ver>/gems/<pkg>-<ver>/` or via bundler |
| Java/Maven | `~/.m2/repository/<pkg-coords>/<ver>/` (JARs; extract for source) |
| Java/Gradle | `~/.gradle/caches/modules-2/files-2.1/<coords>/<ver>/` |
| .NET | `~/.nuget/packages/<pkg>/<ver>/` |

Your LSP usually jumps into these automatically when you "go to definition"
on a third-party symbol. Verify yours does.

## Why Read Dependencies

- **Their docs are incomplete.** Most libraries have ~30% of their behavior
  documented. The rest lives in code.
- **Behavior differs from docs.** Especially for older or unmaintained
  libs.
- **Edge cases are unwritten.** What happens with empty input? Null? Unicode?
  Read the code.
- **Source teaches idioms.** Reading a well-written library is one of the
  best ways to learn a language.
- **Bug hunts often end here.** "Library does the wrong thing" is a real
  finding 5% of the time.

## How to Read Dependency Code

### Find the public entry

The exported surface — `index.js`, `lib.rs`, `__init__.py`, package's
public types — is your entry. Start there.

### Trace one call

Same as your own code: pick a function you use, walk inward, note layers.

### Map the file structure

A small library: one or two files of real logic + supporting infra.
A large library: same shapes you'd find in any project (entry, services,
adapters). Apply the same model-building from
[../01-orientation/mental-model.md](../01-orientation/mental-model.md).

### Check the tests

Dependency tests show "intended use" — sometimes more clearly than docs.

## Pinning and Locking

### Lockfiles are not optional

`package-lock.json`, `pnpm-lock.yaml`, `Cargo.lock`, `poetry.lock`,
`Gemfile.lock`, `go.sum` — these pin transitive dependencies. **Always
commit them** for application repos. **Don't commit them** for libraries
(usually — check ecosystem norms).

### Reproducible installs

The point of lockfiles is that `pnpm install` today produces the same
tree as `pnpm install` next year. If you can't reproduce installs:

- Check that your package manager respects the lockfile (`npm ci`, not
  `npm install`).
- Check that the registry is stable (proxies, mirrors).
- Check for unpinned ranges in transitive deps (modern lockfiles handle
  this; older versions may not).

### Updating dependencies

A whole subspecialty. Quick rules:

- Read the changelog of the dep you're upgrading.
- Run tests after every meaningful upgrade.
- Major version upgrades = expect breakage; allocate time.
- Don't bundle dep upgrades with feature work in the same PR.
- For ecosystems with widespread issues (npm), tools like Renovate or
  Dependabot automate the chore.

## Auditing Dependencies

Especially for new deps you're adding:

### Check the basics

- **License** — see [../13-hidden-knowledge/license-math.md](../13-hidden-knowledge/license-math.md).
- **Maintenance status** — last release date, open issues, recent activity.
- **Bus factor** — single-maintainer libs have risk.
- **Dependency tree size** — adding 1 package can bring in 200.

### Security tools

- `npm audit`, `pnpm audit`, `cargo audit`, `pip-audit`, `bundle-audit`.
- GitHub Dependabot alerts.
- Snyk, GitGuardian for organization-scale scanning.

Treat audit findings seriously but not blindly — assess actual exposure.

### Supply-chain awareness

A few high-impact incidents over the years (event-stream, ua-parser-js,
xz) show that dependency code can be malicious. Awareness, not paranoia:

- Pin to specific versions.
- Read your lockfile after `add`.
- Be more cautious with newly-popular packages, less-popular packages,
  and packages with abrupt maintainer changes.
- Avoid letting CI auto-merge unpinned upgrades.

## Vendoring

Some projects **vendor** their dependencies — copy them into the repo
(`vendor/` directory). Pros: hermetic builds. Cons: huge repos, harder
upgrades.

When working in a vendored project, treat `vendor/` like read-only
project source. *Don't* edit it directly (the next vendor refresh will
overwrite). If you need a fix, fork the upstream library or use a
replace directive.

## Replace / Override Directives

Most ecosystems let you override a dependency for local debugging:

```toml
# Cargo.toml
[patch.crates-io]
my-dep = { path = "../my-dep-fork" }
```

```go
// go.mod
replace github.com/foo/bar => ../bar-fork
```

```json
// package.json (with pnpm)
"pnpm": {
  "overrides": {
    "my-dep": "1.2.3"
  }
}
```

Useful for:
- Debugging into a dependency (point at a local clone).
- Testing a fix to a dep before upstream releases it.
- Emergency patches.

Don't ship code with these in production manifests — always revert
before merging.

## The "Read the Library's Source" Habit

Build the reflex: when a library does something surprising, **open its
source**.

Cost: 5–10 minutes of reading.
Value: deeper understanding, fewer cargo-cult workarounds, occasional
upstream PRs.

## When You Find a Bug in a Dependency

1. **Confirm it's the dep, not your code.** Reproduce with minimal
   example using only the lib.
2. **Search the lib's issues** — known bug?
3. **Read recent commits** — was it just fixed?
4. **Open an issue with reproducer.** Be specific.
5. **If urgent, fork** — work around locally, propose fix upstream, swap
   back when merged.

A submitted fix to a dep you use is often one of the highest-impact
contributions you can make.

## See Also

- [git-archaeology.md](git-archaeology.md) — using `git log` in dep clones
- [../13-hidden-knowledge/license-math.md](../13-hidden-knowledge/license-math.md)
- [../06-contribution/legal.md](../06-contribution/legal.md) — copying code from a dep
