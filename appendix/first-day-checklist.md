# First-Day Checklist

A step-by-step for getting oriented in a repository you've never seen.
Work top to bottom; each step builds on the last. The reasoning behind
each is in [../01-orientation/](../01-orientation/) and
[../02-navigation/](../02-navigation/).

## 1. Read the Front Door

- [ ] Read the **README** fully — purpose, scope, status.
- [ ] Read **CONTRIBUTING.md** — how they want contributions.
- [ ] Skim **CODE_OF_CONDUCT.md**, **GOVERNANCE.md**, **SECURITY.md** if
      present.
- [ ] Check the **LICENSE** — know what you're working with (see
      [../13-hidden-knowledge/license-math.md](../13-hidden-knowledge/license-math.md)).
- [ ] Note the **CHANGELOG** / releases — how active, how recent.

## 2. Read the Map

- [ ] List the top-level directories; form a guess of the structure.
- [ ] Find the **manifest** (`package.json`, `go.mod`, `Cargo.toml`,
      `pyproject.toml`, `pom.xml`) — language, deps, scripts.
- [ ] Find the **entry point(s)** — `main`, the server bootstrap, the CLI
      root (see [../02-navigation/entry-points.md](../02-navigation/entry-points.md)).
- [ ] Identify **config files** — CI, linter, formatter, editor.

## 3. Take the Pulse

- [ ] `git log --oneline -20` — recent activity.
- [ ] `git shortlog -sn | head` — who the main contributors are (bus
      factor).
- [ ] Check open issues/PRs count and recency — is it alive? (see
      [../01-orientation/project-pulse.md](../01-orientation/project-pulse.md))
- [ ] Note response times on recent issues (calibrates patience — see
      [../13-hidden-knowledge/time-zones.md](../13-hidden-knowledge/time-zones.md)).

## 4. Build and Run It

- [ ] Match the **toolchain version** (`.nvmrc`, `.python-version`,
      `rust-toolchain.toml`, JDK version).
- [ ] Install dependencies with the **project's tool** (detect from the
      lockfile — see [../15-language-ecosystems/](../15-language-ecosystems/)).
- [ ] Run the **build**.
- [ ] Run the **test suite** — confirm green *before* you change anything.
- [ ] Run the **app/tool** itself — see it actually work.
- [ ] **Log every place you got stuck** — it's your first docs PR (see
      [../13-hidden-knowledge/docs-as-contribution.md](../13-hidden-knowledge/docs-as-contribution.md)).

## 5. Set Up Your Tools

- [ ] Confirm the **LSP** works — go-to-def, find-references, hover (see
      [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)).
- [ ] Enable **format-on-save** with the project's formatter.
- [ ] Confirm the **linter** runs.
- [ ] Get the **debugger** working — set a breakpoint, hit it.
- [ ] Know the **local CI** commands (lint/test) so you can mirror CI
      before pushing (see [../11-tooling/local-ci.md](../11-tooling/local-ci.md)).

## 6. Build a Mental Model

- [ ] Trace **one real path** end to end (a request, a command, a build).
- [ ] Read the **tests** for the area you'll work in — they encode the
      contract (see [../03-reading-code/tests-as-docs.md](../03-reading-code/tests-as-docs.md)).
- [ ] Identify the **core abstractions** and where they live.
- [ ] Note what's **out of scope** / non-goals (README philosophy, closed
      issues — see [../13-hidden-knowledge/hidden-roadmaps.md](../13-hidden-knowledge/hidden-roadmaps.md)).

## 7. Before You Change Anything

- [ ] **Search existing issues/PRs** for what you're about to do (see
      [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)).
- [ ] For anything non-trivial, **open an issue / discuss first** rather
      than surprising them with a PR (see
      [../06-contribution/issue-vs-pr-first.md](../06-contribution/issue-vs-pr-first.md)).
- [ ] Understand the **branching and commit conventions** (see
      [../06-contribution/branching.md](../06-contribution/branching.md),
      [../06-contribution/commit-conventions.md](../06-contribution/commit-conventions.md)).

## The 15-Minute Version

Short on time? The minimum viable orientation:

1. README + CONTRIBUTING.
2. Manifest (language, scripts) + entry point.
3. `git log --oneline -20`.
4. Build, test, run.
5. Confirm LSP + format-on-save.

## See Also

- [../01-orientation/first-contact.md](../01-orientation/first-contact.md)
- [../01-orientation/build-and-run.md](../01-orientation/build-and-run.md)
- [pre-pr-checklist.md](pre-pr-checklist.md)
- [command-cheatsheet.md](command-cheatsheet.md)
