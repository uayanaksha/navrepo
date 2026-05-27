# Handling CI

Continuous integration is your first reviewer — and the cheapest. A PR
with red CI is not really ready for human review.

## The Discipline

### Watch CI on push

Most editors / `gh` show CI status:

```bash
gh pr checks
gh pr view --web
```

Watch the first ~minute. If CI fails on setup or trivial issues, you
need to fix immediately.

### Fix CI yourself first

Don't ping reviewers to debug your CI failures. That's table stakes.

Exceptions:
- Genuinely flaky tests you can't fix.
- Infrastructure failures.
- CI configuration changes that need maintainer access.

In these cases, *say so*: "CI failure is in the macOS runner; appears
to be infra. Could a maintainer re-trigger?"

### Read the failure carefully

CI output is dense. Look for:
- The first error (often the cause).
- Test names of failures.
- Stack traces.

Many CI logs are noisy; use search to find "FAIL", "Error", or the
keyword.

## Common CI Failures

### Build failure

- Local builds clean, CI fails: env mismatch. Check the workflow file
  for the exact versions and commands.
- Imports / dependencies wrong on CI's environment.

Fix: match your local environment more closely (containers help).

### Test failure

- Test passing locally but failing in CI: timing / env-dependent.
- Test not run locally because of filter.

Fix: run the same test locally with the same command CI uses.

### Lint / format failure

- You forgot to run the linter / formatter.
- Or your local linter version differs from CI's.

Fix: use the project's pre-commit hooks. Pin linter versions.

### Coverage drop

- New code is uncovered.
- Coverage thresholds enforced by CI.

Fix: add tests for the new lines. Don't disable coverage.

### Flake

- The test sometimes passes, sometimes fails.
- Re-running CI without changes makes it pass.

Action:
- Confirm it's a known flake (search the issue tracker).
- If new, file a separate issue tracking it.
- Don't paper over by retrying without investigation.

## Re-Triggering CI

Sometimes you need to re-run CI:

- After a flake.
- After fixing CI config (the workflow itself).

### Push an empty commit (cheap rerun)

```bash
git commit --allow-empty -m "rerun CI"
git push
```

Some projects prefer this for triggering. Squash it before merge.

### Use the UI

GitHub: "Re-run all jobs" in the Actions tab.

### `gh` CLI

```bash
gh run rerun <run-id>
```

Sometimes only maintainers can re-trigger; ask politely if so.

## Skipping CI for Trivial Changes

For docs-only or formatting-only PRs:

```
git commit -m "docs: fix typo [skip ci]"
```

Some projects use `[skip ci]`, `[ci skip]`, or `[no ci]`. Check the
project's convention.

Don't abuse — only for changes that absolutely can't break tests.

## Local CI Mirroring

The single biggest CI hygiene improvement: **run CI's checks locally**
before pushing.

```bash
make ci           # if the project provides this
# or:
make lint && make test && make build
```

Some projects have a `pre-commit` hook configuration:

```bash
pre-commit install
```

Now every commit runs the same checks CI does.

See [../11-tooling/local-ci.md](../11-tooling/local-ci.md).

## Force Pushes During Review

If you `git push --force-with-lease` while review is in progress:

- **GitHub** loses some inline comments (those on lines that no longer
  exist). The general PR comments stay.
- **Reviewers** lose context if they were mid-review.

Best practice:
- During review, push regular commits (not force).
- Before merge, squash if the project squash-merges (automatic).
- Or rebase + squash before final review, then no more force-pushes.

If you must force-push, leave a comment summarizing what changed:

> "Force-pushed after rebasing main. No content changes beyond
> conflict resolution in `service/orders.go`."

## CI Caching

Many CIs cache dependencies (`actions/cache`, etc.). When dep changes
make caching stale, CI is slow. Sometimes you'll need to bust the
cache:

- Update the cache key in the workflow file.
- Or accept slow CI for one run.

You usually don't need to manage this unless you're working on the
workflow files themselves.

## CI Speed Tips

If CI is slow and you're iterating:

- **Run just the affected tests** if the project supports it.
- **Use the `paths-filter`** action to skip jobs that aren't relevant.
- **Run locally first** to avoid push-wait-fail cycles.
- **Parallelize** if the project allows multiple jobs.

But: don't add complexity to the CI itself in your PR. Stick to the
project's CI patterns.

## When CI Catches Real Bugs

CI failing isn't always your fault — sometimes it caught a real bug
in your change. Common:

- A test you forgot to update.
- A platform-specific bug.
- A regression in adjacent code.

Take it seriously. Don't disable the test "to make CI green" — find
the real cause.

## When the Test Is Bad

Sometimes the CI failure is a legitimately bad test (not a flake, but
wrong assertion / outdated expectation).

- Update the test if it's legitimately wrong.
- Explain in the PR: "Updated `test_foo` because it asserted the old
  buggy behavior; expanded to cover the corrected case."

Be honest about why the test changed.

## CI Failures You Don't Control

Some CI failures aren't your code:

- External service down (Docker Hub, package registry).
- CI runner OS issues.
- Infrastructure changes (the workflow needs an update for new
  dependency).

Mention in the PR; ask a maintainer for help. Don't try to fix things
outside your scope.

## CI as Documentation

CI files are also documentation:

- Read `.github/workflows/*.yml` to know what's expected.
- Read job names to know what categories of checks exist.
- The "required" jobs are the ones blocking merge.

A new contributor should be able to read CI files and reproduce the
checks locally.

## See Also

- [../11-tooling/local-ci.md](../11-tooling/local-ci.md) — running CI locally
- [../05-fixing-issues/pre-push-checklist.md](../05-fixing-issues/pre-push-checklist.md) — what to check before push
- [long-running-prs.md](long-running-prs.md) — CI status on long-running PRs
