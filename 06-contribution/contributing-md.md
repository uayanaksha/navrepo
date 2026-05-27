# Reading CONTRIBUTING.md

The most-skipped file in any repo. Reading it carefully is also the
single most reliable way to have your PR accepted.

## Read It Twice

**Once** for procedure: what to do.
**Twice** for tone: what to avoid.

The first read tells you the mechanics. The second tells you the
preferences — what makes a maintainer happy or annoyed.

## What to Look For

### Setup instructions

- Required tools and versions.
- Local environment setup.
- How to build, test, lint.

If these don't match the README, trust CONTRIBUTING.md — it's
contributor-facing.

### Branch and commit conventions

- Branch naming rules.
- Commit message format (Conventional Commits, prefixes, sign-offs).
- Whether commits should be squashed before merge or after.

### PR process

- Open an issue first? When?
- Need to be assigned to an issue?
- Which target branch (`main`, `dev`, `next`)?
- PR title format?
- Required PR body sections?
- CLA / DCO requirements?

### Review process

- Who reviews?
- How long is typical?
- How to ping?
- What labels mean ("needs-review", "needs-changes", "ready-to-merge").

### Code style

- Linter / formatter to run.
- Style rules beyond the linter.
- Naming conventions.
- File organization.

### Testing expectations

- Required test coverage.
- Which test types are valued (unit, integration, e2e).
- How to add tests for specific features.

### What gets rejected

Some CONTRIBUTING files explicitly say "we don't accept X":

- No new features without an RFC.
- No PRs touching the legacy module.
- No formatting-only changes.
- No changes affecting downstream-specific behavior.

Read these. Don't waste your time on rejected work.

## Tone Markers

The way CONTRIBUTING.md is written says a lot:

- **Detailed, warm tone**: maintainers value contributors; engagement
  likely.
- **Curt, rule-heavy**: maintainers are stretched; follow rules
  carefully.
- **"We don't have time for X"**: project is overloaded; bring small
  PRs.
- **Long lists of expectations**: high bar; calibrate accordingly.
- **Frequent updates**: process is evolving; check before each PR.
- **Stale, 5 years old**: process may not match reality; ask in chat.

## Sections Common Enough to Memorize

### "First-time contributors"

Some projects have a special path:

- "Look for `good first issue` labels."
- "We're happy to mentor on these."

If you're new to the project, start there.

### "Help wanted"

Issues the maintainers explicitly want help on. Higher acceptance
probability.

### "Out of scope"

What the project won't do. Don't propose these.

### "Definition of done"

What the maintainer considers a complete PR. Often includes:

- Tests added.
- Docs updated.
- CHANGELOG entry.
- Reviewers approved.
- CI green.

Match these before requesting review.

## Project-Specific Files Beyond CONTRIBUTING

### `STYLE.md` / `STYLEGUIDE.md`

Beyond the linter, often captures naming, structure, comment style.

### `DEVELOPMENT.md`

Day-to-day developer environment. May include debugging tips.

### `TESTING.md`

Test conventions, mock setup, integration test environment.

### `RELEASING.md`

Release process. Usually maintainer-only but tells you the cadence.

### `docs/contributing/`

For larger projects, contribution docs span a folder.

Read them all if they're brief; sample them if not.

## When CONTRIBUTING.md Is Missing

Some projects don't have one. Then:

- Look at recent merged PRs for implicit conventions.
- Check `.github/pull_request_template.md`.
- Look at issue templates for hints.
- Look at the README for any contribution notes.
- Sample 5–10 recent commits for message style.

Ask in chat / discussions if process matters and isn't documented.
"Hi, I'd like to contribute X — anything I should know about
process?" works.

## When CONTRIBUTING.md Contradicts Itself

Sometimes the document is stale or self-contradicting. Sample current
PRs:

- "CONTRIBUTING says squash; PRs are merge commits. Which is current?"

Ask. Don't guess.

## Updating CONTRIBUTING.md as a Contribution

If you find CONTRIBUTING.md missing, stale, or unclear during your work,
fixing it is often welcomed. Especially:

- "Setup steps were missing X."
- "The test command in the docs is wrong."
- "Add a 'common pitfalls' section."

These are low-stakes, high-value PRs.

## Some Real-World Project Patterns

A few common patterns to recognize:

### "Issue first" projects

```
1. Open an issue describing the bug or proposal.
2. Wait for maintainer triage.
3. Once labeled "accepted," open a PR.
```

Don't skip step 1.

### "CLA bot" projects

A bot checks for CLA sign-off on PRs. Sign it on first PR; auto-checks
forever after.

### "Per-component reviewers" projects

CODEOWNERS specifies who reviews each path. Your PR auto-requests their
review.

### "Discussion first for non-trivial" projects

```
For PRs > 100 lines, open a discussion first.
```

These projects burn out reviewers; respect the threshold.

## Common Mistakes Caught by Reading CONTRIBUTING

- Wrong target branch (PR'd against `main`, project uses `dev`).
- Wrong commit format (no sign-off, wrong prefix).
- Missing changelog entry.
- Skipping the "open an issue first" step.
- Not running the project's full test suite.
- Missing license/CLA acknowledgment.

Every one of these costs a review cycle.

## See Also

- [search-before-filing.md](search-before-filing.md)
- [issue-vs-pr-first.md](issue-vs-pr-first.md)
- [../01-orientation/repo-files.md](../01-orientation/repo-files.md)
