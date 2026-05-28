# 15 — Language Ecosystems

> Every language has its own package manager, its own build tool, its
> own test runner, and its own set of gotchas that bite newcomers. A
> few minutes of ecosystem-specific orientation saves hours.

The general skills in this manual transfer across languages. The
*specifics* don't: how you install dependencies, run tests, format code,
and the traps that catch outsiders all differ. This section is the
quick-start-and-gotchas card for the major ecosystems — enough to be
productive in an unfamiliar one fast.

## Contents

| File | What you'll learn |
|---|---|
| [python.md](python.md) | uv/poetry/pip, virtualenvs, pytest, ruff, type checkers, gotchas |
| [javascript-typescript.md](javascript-typescript.md) | npm/pnpm/yarn/bun, tsconfig, bundlers, monorepos, gotchas |
| [go.md](go.md) | modules, gofmt, go test, pprof, linters, gotchas |
| [rust.md](rust.md) | cargo, rustup, clippy, rust-analyzer, gotchas |
| [java-kotlin.md](java-kotlin.md) | Maven/Gradle, JVM tooling, profilers, gotchas |
| [cpp.md](cpp.md) | CMake/Bazel, compilers, sanitizers, package managers, gotchas |
| [multi-language-monorepos.md](multi-language-monorepos.md) | Bazel/Buck, per-language sandboxing, cross-language refactors |

## How to Use This Section

You don't read it front to back. When you land in a repo of language X,
read X's page, set up the toolchain, and skim the gotchas so you don't
fall into the well-known traps. Then get to work.

## The One-Sentence Summary

> **Learn each ecosystem's package manager, test command, and top three
> gotchas — that's 80% of being productive in it.**

## See Also

- [../09-unknown-tech/](../09-unknown-tech/) — the general "new tech" playbook
- [../11-tooling/](../11-tooling/) — language-agnostic tooling
- [../01-orientation/build-and-run.md](../01-orientation/build-and-run.md) — first-run orientation
