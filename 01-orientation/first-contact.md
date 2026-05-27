# First Contact: The 30-Minute Recon

You've just cloned a repo. Don't start typing changes. Don't start reading
code top-to-bottom. Do this instead.

## The Protocol

Run these in order. Skipping steps costs you days later.

### Step 1 — Read the README (5 min)

End-to-end. Note:

- **Stated purpose** — what problem does it solve, for whom?
- **Stated non-goals** — sometimes explicit, often implicit ("we don't do X").
- **Status** — alpha, beta, stable, deprecated, archived?
- **Quick start** — the path the maintainers want you to walk first.

Red flags worth pausing on:
- README hasn't been updated in years.
- README contradicts the codebase (mentions tools/files that don't exist).
- README is purely marketing with no usage info.

### Step 2 — Build it (5–15 min)

Whatever the project's build is — `make`, `npm install && npm build`,
`cargo build`, `./gradlew build` — run it.

If the build fails, **fixing your environment is step zero**. Do not skip
this and "read the code anyway." A repo you can't build is a repo you
can't change.

Common build snags and where they hide:
- Wrong language version → check `.nvmrc`, `.python-version`, `rust-toolchain.toml`.
- Wrong package manager → `pnpm-lock.yaml` ≠ `package-lock.json` ≠ `yarn.lock`.
- Missing system deps → check the README's "prerequisites" or CI config.
- Wrong OS assumptions → some projects assume Linux; check Docker/devcontainer support.

See [build-and-run.md](build-and-run.md) for deeper troubleshooting.

### Step 3 — Run the tests (5 min)

Find and run them. Note:
- How long they take. (30s vs 30min shapes your dev loop.)
- Which framework/runner. (Tells you what the project's vocabulary is.)
- Where they live. (`tests/`, `__tests__/`, alongside source, etc.)
- Whether there are integration/e2e tests, and how to skip them.

If tests fail on a fresh clone — that's a project-health signal. Look at
recent commits or open issues; might be a known flake or might be a real
problem.

### Step 4 — Skim the tree (5 min)

```bash
tree -L 2 -I 'node_modules|.git|target|dist|build|vendor' .
# or, if tree isn't installed:
find . -maxdepth 2 -type d -not -path '*/node_modules*' -not -path '*/.git*'
```

Look for:
- **Source layout** — is it flat, layered, hexagonal, DDD?
- **Tests directory** — central or co-located?
- **Configuration** — `config/`, `.env.example`, `settings/`?
- **Docs** — is there a `docs/` folder? Is it generated or human-written?
- **Scripts** — `scripts/`, `bin/`, `tools/` often contain operational knowledge.

### Step 5 — Read the CHANGELOG (5 min)

Look at the last **10 entries** (or last quarter if entries are sparse).
This is the project's recent diary. You learn:

- **What it cares about right now** — features, fixes, refactors?
- **Velocity** — weekly releases vs annual?
- **Breaking changes** — frequency tells you how stable the project is.
- **Vocabulary** — what subsystems they refer to.

If there's no CHANGELOG, read recent merged PRs:

```bash
gh pr list --state merged --limit 20
git log --oneline --merges -20
```

## After 30 Minutes, You Should Know

- [ ] The project's purpose, in one sentence you didn't read from the README.
- [ ] How to build it.
- [ ] How to run its tests.
- [ ] Where the entry point is.
- [ ] Where tests live.
- [ ] What's been worked on lately.
- [ ] Whether the project is healthy enough to contribute to.

If you can't check all these boxes, repeat the recon. Don't proceed to
code changes.

## What NOT to Do

- ❌ Open `main.go` / `index.js` / `app.py` and start reading top-to-bottom.
- ❌ Search for an issue and dive into "fix mode."
- ❌ Open the file mentioned in a bug report and try to understand it cold.
- ❌ Skip building because "I just want to read."
- ❌ Skip tests because "they're probably fine."

Every shortcut here costs hours later.

## See Also

- [repo-files.md](repo-files.md) — decoding the standard files
- [build-and-run.md](build-and-run.md) — getting unstuck when build breaks
- [project-pulse.md](project-pulse.md) — reading project health
- [mental-model.md](mental-model.md) — what to build in your head after recon
