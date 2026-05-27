# Repository Files Decoded

Every well-maintained repo carries a set of "meta" files that tell you who
the project is, how it works, and how to engage with it. Most people skim
them. Reading them carefully is one of the highest-leverage uses of your
time.

## The Canonical Set

| File | Purpose | Read priority |
|---|---|---|
| `README.md` | Project purpose, audience, quick start | Always |
| `CONTRIBUTING.md` | Process expectations | Twice, before any PR |
| `CODE_OF_CONDUCT.md` | Behavioral norms | Skim |
| `CHANGELOG.md` | Release diary | Last 10 entries |
| `LICENSE` | What you can legally do | Always, before using |
| `SECURITY.md` | How to report vulnerabilities | Before filing security issues |
| `CODEOWNERS` (in `.github/` or root) | Who reviews what | If your PR is non-trivial |
| `MAINTAINERS.md` / `OWNERS` | Sometimes formal, often informal | If exists |
| `GOVERNANCE.md` | How decisions get made | If contributing meaningfully |
| `ARCHITECTURE.md` | Intended mental model | Always, if it exists |
| `ROADMAP.md` | Stated future direction | Read for context |
| `SUPPORT.md` | Where to get help | When stuck |

If a file in this list exists, read it. It's there on purpose.

## File-by-File

### README.md

The front door. Should cover:

1. **What** the project is (one sentence).
2. **Why** it exists / what problem it solves.
3. **Who** it's for.
4. **How** to install or get started.
5. **Where** to find more.

When evaluating:
- Is it concise or rambling? (Sign of writer-discipline maturity.)
- Are the examples copy-paste runnable?
- Are version numbers / dates current?
- Does it link to deeper docs or expect you to live in the README forever?

### CONTRIBUTING.md

Your single most important meta-file. Look for:

- **Setup steps** — what you need locally.
- **Branch and commit conventions** — names, message format, sign-offs.
- **PR process** — issue first? CLA? labels?
- **Style rules** — beyond what linters enforce.
- **Communication norms** — where to ask questions.
- **What gets rejected** — sometimes explicit ("no new features without RFC").

**Read it twice.** Once for procedure, once for tone. Tone reveals
maintainer preferences that aren't formally written.

### CHANGELOG.md

Reading the last 10–20 entries teaches you:

- **What they ship** — features vs fixes vs refactors.
- **Vocabulary** — names of subsystems they care about.
- **Velocity** — release cadence.
- **Compatibility stance** — frequency of "breaking change" notes.

A project that hasn't updated its CHANGELOG in 6 months but has commits
weekly is often a project where the CHANGELOG is auto-generated or
discarded. Check release notes on the repo host instead.

### CODE_OF_CONDUCT.md

Mostly boilerplate from the Contributor Covenant or similar. Read for:

- **Enforcement contact** — who handles violations.
- **Special norms** — some communities add their own values.

Don't ignore it just because it's boilerplate. Maintainers reference it
when conversations go sideways.

### LICENSE

Critical. Know:

- **What you can copy in.** Is the project's license compatible with code
  you'd paste from elsewhere?
- **What you can do with the project.** GPL means downstream is GPL.
  Permissive means you can ship proprietary derivatives.
- **License changes.** A repo whose license changed recently
  (`git log -p -- LICENSE`) often signals commercial repositioning.

Common licenses, briefly:
- **MIT, BSD-2/3, Apache 2.0** — permissive, do almost anything.
- **LGPL, MPL** — weak copyleft, modifications to the library are shared.
- **GPL, AGPL** — strong copyleft, derivatives share alike.
- **BSL, SSPL, Elastic** — source-available, restrictions on commercial use.

Apache 2.0 is unusually friendly — it includes an explicit patent grant.

### SECURITY.md

Vulnerabilities don't go in the public issue tracker. SECURITY.md tells
you the disclosure channel: an email, a PGP key, a bug bounty platform.

**Always read this before filing anything that might be a security issue.**
"Maybe a vuln?" gets the security channel; "definitely not" goes public.
When in doubt, choose private.

### CODEOWNERS

A GitHub feature; lives in `.github/CODEOWNERS`, `docs/CODEOWNERS`, or
root. Each pattern maps to one or more reviewers who get auto-requested.

Reading it tells you:
- **Who owns what** — when your PR touches `pkg/auth/`, expect `@security-team` on review.
- **Who has authority** — repeat names across many paths = senior maintainers.
- **Boundaries** — folders without an owner are likely "ambient" / shared.

### ARCHITECTURE.md / docs/architecture/

When it exists, this is gold. It encodes the *intended* mental model — the
one the maintainers want you to have. Worth more per minute than reading
code.

Even better: **ADRs** (Architecture Decision Records), usually in
`docs/decisions/` or `docs/adr/`. Each ADR captures one decision with
context, alternatives, and rationale. Read them in date order; you'll
absorb the project's history of trade-offs.

### .github/ Directory

Often skipped. Contains:

- **Issue templates** (`ISSUE_TEMPLATE/`) — tells you what info maintainers want.
- **PR templates** (`pull_request_template.md`) — fill this in, don't replace it.
- **Workflows** (`workflows/`) — the actual CI. Read to know what runs before merge.
- **Dependabot config** (`dependabot.yml`) — dependency policy.
- **FUNDING.yml** — how the project is funded.

CI workflows are particularly informative — they show you, in concrete
commands, exactly what the project considers "passing."

### Makefile / justfile / package.json scripts

The project's **vocabulary in commands**. `make help`, `just`, or
`npm run` shows the canonical entry points. Use them; don't reinvent.

If `make test` exists, run that, not `pytest` directly. Otherwise you're
just running tests differently from CI.

## Files That *Aren't* in the List But Should Be Read

### `.editorconfig`

Encodes formatting rules editors apply automatically. If your editor
doesn't pick it up, install the plugin. You'll save a lot of churn.

### `.gitignore`

Reveals what's transient (build output, caches, env files). Don't commit
anything that matches these patterns.

### `.tool-versions` / `.nvmrc` / `.python-version` / `rust-toolchain.toml`

Pinned language versions. Use the matching version manager (`asdf`, `nvm`,
`pyenv`, `rustup`) — don't fight it.

### `pre-commit` config (`.pre-commit-config.yaml`)

If present, install pre-commit hooks before your first commit:

```bash
pre-commit install
```

Saves you from CI failures over trivial formatting.

### `docker-compose.yml` / `devcontainer.json`

Hints that the project expects containerized development. Use it; don't
fight it. Often the *only* supported environment.

## Reading Order, Compressed

When time-boxed to 10 minutes:

1. README (skim)
2. CONTRIBUTING (sections: setup, PR process, conventions)
3. CHANGELOG (last 10 entries)
4. ARCHITECTURE.md if it exists, else `docs/` index

That's enough to start. Read the rest as you encounter need.

## See Also

- [first-contact.md](first-contact.md) — where these reads fit in the recon flow
- [project-pulse.md](project-pulse.md) — what these files imply about project health
- [../13-hidden-knowledge/governance.md](../13-hidden-knowledge/governance.md) — when GOVERNANCE.md actually matters
