# Local CI

The worst feedback loop is: push, wait eight minutes, watch CI fail on
something a linter would have caught in two seconds. Move the checks
left — run them before you push.

## The Goal

Whatever CI runs, you should be able to run a fast subset locally
*before* pushing, and the full suite locally *when you need to*. The
closer your local checks mirror CI, the fewer red-X surprises.

## Step One: Read the CI Config

Before mirroring CI, know what it does. The config tells you:

```bash
# GitHub Actions
ls .github/workflows/
cat .github/workflows/ci.yml

# GitLab
cat .gitlab-ci.yml

# Others
cat .circleci/config.yml  Jenkinsfile  .buildkite/pipeline.yml  azure-pipelines.yml
```

Look for the actual commands: the `run:` steps. Those are what you
want to reproduce. Often it's just `make test`, `npm run ci`, or a
handful of lint/test/build invocations.

Many projects wrap the canonical checks in one target:

```bash
make lint test       # or
make ci              # or
just check           # (Justfile)
npm run verify
```

Run that target locally and you've reproduced most of CI.

## pre-commit (the Framework)

`pre-commit` is a language-agnostic hook manager. A committed
`.pre-commit-config.yaml` defines checks; everyone on the repo gets the
same ones.

```bash
pipx install pre-commit       # or brew, or pip
pre-commit install            # wires up the git hook
```

Now relevant checks run automatically on `git commit`, touching only
changed files. To run across the whole repo:

```bash
pre-commit run --all-files
```

A typical config:

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.0
    hooks:
      - id: ruff
      - id: ruff-format
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-merge-conflict
```

If a repo ships a `.pre-commit-config.yaml`, install it. It's the
maintainer telling you exactly which checks they expect to pass.

## act — Run GitHub Actions Locally

[`act`](https://github.com/nektos/act) runs GitHub Actions workflows
in local Docker containers. Useful when CI does something complex you
can't easily replicate by hand.

```bash
act                          # run the push event's workflows
act -j test                  # run only the `test` job
act pull_request             # simulate a PR event
act -l                       # list jobs without running
```

Caveats:
- It uses Docker images that approximate GitHub's runners, not exact
  copies. Subtle environment differences remain.
- Some actions (especially ones using GitHub's API or secrets) won't
  fully work locally.
- It can be slow on first run (pulling large runner images).

`act` is best for "does my workflow YAML even parse and run," not for
perfectly reproducing a flaky CI failure.

## Mirroring CI by Hand

Often simpler than `act`: just run the same commands in the same order
CI does. Make a local script:

```bash
# scripts/ci-local.sh
set -euo pipefail
ruff check .
ruff format --check .
mypy src/
pytest -q
```

Run it before every push. If it's green, CI is very likely green.

The discipline: when CI fails on something your script didn't catch,
*add that check to your script*. Over time it converges on CI.

## Matching the Environment

Most local-vs-CI discrepancies come from environment drift, not the
commands. The usual suspects:

| Drift | Fix |
|---|---|
| Different language version | Use the repo's version pin (`.python-version`, `.nvmrc`, `rust-toolchain.toml`, `.tool-versions`) |
| Different dependency versions | Install from the lockfile, not loosely |
| Missing env vars | Check CI config's `env:` block |
| OS differences (Linux CI, macOS local) | Run in a container, or test the OS-specific path explicitly |
| Locale / timezone | CI often runs UTC; set `TZ=UTC` locally to match |

Version managers that read the repo's pin file:

```bash
mise install      # reads .tool-versions / mise.toml (multi-language)
asdf install      # reads .tool-versions
nvm use           # reads .nvmrc
pyenv local       # reads .python-version
rustup show       # respects rust-toolchain.toml
```

See [../04-reproducing-issues/environment-parity.md](../04-reproducing-issues/environment-parity.md)
for the deeper version of this.

## Watch Mode: The Inner Loop

While developing, don't even run checks manually. Watch the files:

```bash
watchexec -e py -- pytest tests/test_thing.py     # generic
cargo watch -x test                                # Rust
npm run test -- --watch                            # Jest/Vitest
go test ./... ; # with reflex/air for watching
ptw                                                # pytest-watch (Python)
```

The loop becomes: save file → tests run → see result. Seconds, not
minutes.

## Containerized Local CI

For maximum parity, run CI's steps inside the same base image CI uses:

```bash
docker run --rm -v "$PWD":/app -w /app python:3.12-slim \
    bash -c "pip install -e .[dev] && pytest -q"
```

Slower, but eliminates "works on my machine" almost entirely. Reserve
it for chasing environment-specific failures, not the everyday loop.

## The Layered Strategy

Run checks in increasing cost order, failing fast:

1. **On save** (watch mode): the one test file you're editing.
2. **On commit** (`pre-commit`): format + lint on changed files.
3. **Before push** (local script / `pre-push` hook): full lint + test.
4. **In CI**: the authoritative full matrix (all OSes, all versions).

You only pay the slow step when the fast steps pass. CI becomes
confirmation, not discovery.

## Anti-Patterns

### Using CI as your test runner

Pushing half-baked commits to "see if CI passes" wastes minutes per
cycle, clutters history, and annoys anyone watching the branch. Run it
locally first.

### `--no-verify` as a habit

If you routinely skip hooks, your hooks are too slow or too noisy. Fix
the hooks; don't bypass them. (Bypassing also means the broken thing
reaches CI anyway.)

### Local checks that drift from CI

A local script that's missing half of CI's checks gives false
confidence. Keep them in sync — ideally CI *calls the same script* you
run locally.

### Over-investing in `act`

If `make test` reproduces CI, you don't need to emulate the whole
Actions runner. Reach for `act` only when the workflow logic itself is
what's failing.

## See Also

- [git-config.md](git-config.md)
- [shell-and-cli.md](shell-and-cli.md)
- [../04-reproducing-issues/environment-parity.md](../04-reproducing-issues/environment-parity.md)
- [../07-pull-requests/handling-ci.md](../07-pull-requests/handling-ci.md)
- [../05-fixing-issues/pre-push-checklist.md](../05-fixing-issues/pre-push-checklist.md)
