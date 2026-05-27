# Environment Parity

"Works on my machine" is a real phenomenon, not a meme. Most often it's
environment drift — your machine differs from the reporter's, or from
production, in a way that matters.

## The Variables That Matter

Roughly in order of how often they cause divergence:

1. **Language version** (Python 3.10 vs 3.11; Node 18 vs 20)
2. **Dependency versions** (especially transitives)
3. **OS** (Linux vs macOS vs Windows)
4. **OS version / kernel**
5. **CPU architecture** (amd64 vs arm64)
6. **Locale and timezone**
7. **File system semantics** (case sensitivity, line endings)
8. **Network behavior** (proxies, DNS, IPv6)
9. **System packages** (libssl, libxml2)
10. **Hardware quirks** (memory, network jitter)

## How to Pin

### Language version

| Ecosystem | Pin file | Tool |
|---|---|---|
| Node | `.nvmrc` | nvm, fnm |
| Python | `.python-version`, `pyproject.toml` | pyenv, uv, rye |
| Ruby | `.ruby-version` | rbenv |
| Rust | `rust-toolchain.toml` | rustup (auto) |
| Go | `go.mod` (`go 1.22`) | gvm or direct |
| Java | `.sdkmanrc` | sdkman, jenv |

**Commit these.** And use a version manager that respects them.

### Dependency versions

Always **commit lockfiles** for applications:

- `package-lock.json` / `pnpm-lock.yaml` / `yarn.lock`
- `Cargo.lock`
- `poetry.lock` / `uv.lock` / `pip-tools` requirements.txt
- `Gemfile.lock`
- `go.sum`

Use the "install from lockfile" command (`npm ci`, not `npm install`)
in CI.

### Container as ground truth

If the project has Docker/devcontainer support, that's the canonical
env:

```bash
docker compose run --rm app sh
# or
devcontainer up
```

Everything inside matches. If a bug repros there but not outside, it's
environment drift.

## Detecting Drift

When investigating "works for me, not for them":

### Compare versions

Ask (or check):

```bash
# Node
node --version
npm --version

# Python
python --version
pip list | grep <suspect-pkg>

# Rust
rustc --version
cargo --version

# Go
go version

# OS
uname -a
cat /etc/os-release
```

A `diff` between your output and theirs often reveals the cause.

### Compare environments

Environment variables matter:

```bash
env | sort > my-env.txt
# get reporter's env (sanitized)
diff my-env.txt their-env.txt
```

Watch for: `LANG`, `LC_*`, `TZ`, `PATH`, language-specific (`PYTHONPATH`,
`NODE_OPTIONS`), proxy settings.

### Compare file systems

- macOS default: case-insensitive HFS+/APFS.
- Linux default: case-sensitive ext4.

Code that works on Mac may fail on Linux if a filename is `Foo.txt`
referenced as `foo.txt`. CI on Linux catches this; local dev on Mac
doesn't.

Line endings:
- Windows: CRLF.
- Linux/Mac: LF.

`.gitattributes` controls this. Without one, mixed line endings sneak in.

## Common Environment Bugs

### Timezone

```python
datetime.now()  # uses local time
datetime.utcnow()  # uses UTC
```

A test that passes in UTC may fail in PST because dates roll over
differently. Always store and compare in UTC; only convert at the
boundary.

### Locale-dependent sorting

```python
sorted(["a", "B", "c"])
# ['B', 'a', 'c']  in C locale
# ['a', 'B', 'c']  in en_US.UTF-8
```

Tests written in one locale fail in another. Set `LC_ALL=C` or
explicitly Unicode-collate.

### Number formatting

```python
"1,000.5"  # English
"1.000,5"  # German
```

Code parsing numbers should be locale-explicit.

### Dependency divergence

A package may behave differently on different platforms:

- `chokidar` (file-watching) on macOS vs Linux.
- Some Python C extensions only ship for amd64, not arm64.
- Node native modules need to be rebuilt per arch.

If a dep behaves differently, you'll see it in install logs.

## CI as Canonical Environment

CI runs on a known, clean environment. If a bug repros in CI but not
locally:

- **Trust CI** — it's the more reliable reference.
- **Read the CI workflow file** to learn its exact setup.
- **Try to replicate locally** (Docker often helps).

Some teams use `act` to run GitHub Actions locally:

```bash
act -j test
```

Imperfect but useful.

## When You Don't Control the Environment

For OSS projects where users run on their own machines:

- **Document supported environments** explicitly.
- **Test on multiple OSes in CI** when feasible.
- **Ask for environment details** in bug reports (template asks for OS,
  versions).
- **Default to "won't fix" for unsupported envs** — politely.

## Pre-Repro Environment Capture

When you do reproduce, capture your environment:

```bash
# snapshot.sh
#!/usr/bin/env bash
{
    uname -a
    node --version
    cat .nvmrc
    npm list --depth=0
    env | grep -E '^(NODE|NPM|PATH|LANG|TZ)='
} > env-snapshot.txt
```

Include this in your bug report or PR description. Saves the next
debugger hours.

## Dev Container / Codespaces

For projects with `devcontainer.json`:

```bash
# VS Code:
> Dev Containers: Reopen in Container

# Or:
devcontainer up
```

Use them. They eliminate most env drift.

For projects without: a single `Dockerfile.dev` or `docker-compose.yml`
contribution often gets welcomed.

## Anti-Patterns

- **Manually installing system deps and forgetting them.** Use Brewfile
  / apt-packages.txt / containers.
- **Mixing version managers.** Don't have nvm and asdf both managing
  Node — they fight.
- **Trusting "global" tools.** A `python` on PATH may not be the one
  the project pins.
- **Ignoring lockfile drift.** A lockfile that conflicts every PR is a
  smell — usually wrong dep manager mode.

## See Also

- [minimal-reproduction.md](minimal-reproduction.md) — env is part of MRE
- [../01-orientation/build-and-run.md](../01-orientation/build-and-run.md) — env setup at the start
- [../11-tooling/local-ci.md](../11-tooling/local-ci.md) — matching CI locally
