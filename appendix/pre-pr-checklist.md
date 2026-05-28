# Pre-PR Checklist

Run through this before you open a pull request. It's the expanded,
contribution-facing version of
[../05-fixing-issues/pre-push-checklist.md](../05-fixing-issues/pre-push-checklist.md).
Five minutes here saves a review cycle (or three).

## The Change Itself

- [ ] **It does one thing.** Could you summarize the PR in one sentence?
      If not, split it (see [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)).
- [ ] **No drive-bys.** No unrelated cleanups, renames, or "while I was
      here" changes (see [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)).
- [ ] **Root cause, not symptom.** You fixed the actual problem, not just
      the visible effect (see [../05-fixing-issues/root-cause-vs-symptom.md](../05-fixing-issues/root-cause-vs-symptom.md)).
- [ ] **Backwards compatible** (or the break is intentional and noted —
      see [../05-fixing-issues/backwards-compatibility.md](../05-fixing-issues/backwards-compatibility.md)).
- [ ] **Minimal surface area.** No new flags/options/APIs beyond what's
      needed (see [../13-hidden-knowledge/maintainer-calculus.md](../13-hidden-knowledge/maintainer-calculus.md)).

## Tests

- [ ] **A test that fails without your change** and passes with it (for a
      bugfix, this proves the fix — see [../05-fixing-issues/test-first-fixing.md](../05-fixing-issues/test-first-fixing.md)).
- [ ] **Edge cases covered** — null/empty/large/concurrent, as relevant.
- [ ] **The whole suite passes** locally.
- [ ] **No flaky/commented-out/skipped tests** left behind.

## Quality Gates (Mirror CI)

- [ ] **Formatter** run (or format-on-save did it).
- [ ] **Linter** passes.
- [ ] **Type checker** passes (if the project has one).
- [ ] **Build** is clean — no new warnings.
- [ ] Ran the project's **CI-equivalent command** locally (see
      [../11-tooling/local-ci.md](../11-tooling/local-ci.md)).

## Self-Review the Diff

- [ ] **Read your own diff** line by line — you'll catch debug prints,
      stray TODOs, commented code, typos (see [../07-pull-requests/self-review.md](../07-pull-requests/self-review.md)).
- [ ] **No secrets** — keys, tokens, `.env`, credentials, internal URLs.
- [ ] **No accidental files** — build artifacts, editor configs, large
      binaries, debug logs.
- [ ] **Whitespace/format noise** isn't drowning the real change.

## Commits

- [ ] **Commit messages** follow the project's convention (Conventional
      Commits? — see [../06-contribution/commit-conventions.md](../06-contribution/commit-conventions.md)).
- [ ] **History is appropriate** for the project's merge strategy — clean
      commits if they preserve them, don't bother if they squash (see
      [../13-hidden-knowledge/merge-strategies.md](../13-hidden-knowledge/merge-strategies.md)).
- [ ] **Signed off / signed** if the project requires DCO/signing (see
      [../06-contribution/legal.md](../06-contribution/legal.md)).
- [ ] **Rebased on / merged latest** target branch; conflicts resolved.

## The PR Description

- [ ] **What and why**, not just what. Link the issue it closes.
- [ ] **How you tested it** — the test plan (see [../07-pull-requests/pr-description.md](../07-pull-requests/pr-description.md)).
- [ ] **Visual artifacts** for UI changes — before/after screenshots or a
      clip (see [../07-pull-requests/visual-artifacts.md](../07-pull-requests/visual-artifacts.md)).
- [ ] **Scope/limitations** noted — what's intentionally *not* included.
- [ ] **Follows the PR template** if one exists.
- [ ] **Title** is clear and convention-conforming (it may become the
      squash commit message).

## Context Checks

- [ ] **Discussed first** if it's non-trivial — no surprise large PRs
      (see [../06-contribution/issue-vs-pr-first.md](../06-contribution/issue-vs-pr-first.md)).
- [ ] **Fits the project direction** — not a known non-goal (see
      [../13-hidden-knowledge/hidden-roadmaps.md](../13-hidden-knowledge/hidden-roadmaps.md)).
- [ ] **Right repo** — the bug isn't actually in a dependency (see
      [../13-hidden-knowledge/right-repo-problem.md](../13-hidden-knowledge/right-repo-problem.md)).
- [ ] **Not a security issue** — those go through private disclosure, not
      a public PR (see [../13-hidden-knowledge/security-disclosure.md](../13-hidden-knowledge/security-disclosure.md)).
- [ ] **Reasonable size** — under ~400 lines of review where possible;
      consider a draft or a stack if larger (see [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md)).

## After Opening

- [ ] **Watch CI** and fix failures promptly (see [../07-pull-requests/handling-ci.md](../07-pull-requests/handling-ci.md)).
- [ ] **Re-read it on the platform** — the rendered diff catches things
      your editor didn't.
- [ ] **Be patient** for review; one polite follow-up after a real
      interval, not daily pings (see [../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md)).

## See Also

- [../05-fixing-issues/pre-push-checklist.md](../05-fixing-issues/pre-push-checklist.md)
- [../07-pull-requests/self-review.md](../07-pull-requests/self-review.md)
- [first-day-checklist.md](first-day-checklist.md)
- [pr-discussion-phrases.md](pr-discussion-phrases.md)
