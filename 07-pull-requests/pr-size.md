# PR Size

A reviewer can hold ~200–400 lines in their head before quality drops.
Above ~600 lines, real review stops happening — the maintainer either
rubber-stamps or stalls.

## Why Small PRs Win

- **Faster review.** A reviewer with 30 minutes can finish a 200-line
  PR but not a 2000-line one.
- **Better review.** Holding everything in head is the difference between
  catching a bug and missing it.
- **Less back-and-forth.** Small PRs converge in 1–2 rounds. Large PRs
  spiral.
- **Easier rollback.** If one of 10 small PRs has a bug, you revert it.
  If one 5000-line PR has a bug, you can't easily extract.
- **Continuous integration.** Many small landings let CI catch issues
  earlier.

## Size Targets

Approximate, project-dependent:

| Size | Reviewer experience |
|---|---|
| 1–50 lines | Quick approval, often single round |
| 50–200 lines | Real review, fast turnaround |
| 200–400 lines | Substantive review, may need a couple rounds |
| 400–800 lines | Reviewer fatigue starts; quality drops |
| 800+ lines | Often rubber-stamped or stalled |

Aim for under 400. Tolerate up to ~800 occasionally.

## What Counts as "Lines"

Significant lines, not formatting or generated:

```bash
git diff --stat main...HEAD
# Excludes: trailing newlines but counts actual change.
```

For real "review burden," look at:

- Production code changed (most weight).
- Tests added (some weight).
- Generated code (almost no weight if clearly marked).
- Docs (light weight).
- Vendored / lock-file changes (negligible).

A PR with 1200 lines but 1100 in `package-lock.json` is reviewable. A
PR with 600 lines of code is not.

## Strategies for Smaller PRs

### Split by phase

For a feature that needs:
1. Type / schema additions.
2. Business logic.
3. Endpoint wiring.
4. Tests.
5. Documentation.

Land them as separate PRs in order. Each is small.

### Refactor first, feature second

```
PR1: Extract `OrderService` from existing inline logic. No behavior change.
PR2: Add new order type using the extracted service.
```

Reviewers can verify "PR1 changes nothing" mechanically.

### Land scaffolding behind a flag

```
PR1: Add new code paths, gated behind `new_oauth=true` flag (default off).
PR2: Tests that exercise the flag.
PR3: Flip default once stable.
PR4: Remove old path and flag.
```

Each PR small, low-risk, reversible.

### Land tests with examples first

```
PR1: Add failing tests demonstrating desired behavior.
PR2: Implement to make tests pass.
```

Some maintainers love this (separates "what should happen" from
"how"). Some don't (CI fails on PR1).

Ask first.

## Stacked PRs

For sequential changes, see [stacked-prs.md](stacked-prs.md).

## When the PR Is Necessarily Large

Sometimes you can't shrink:

- A single rename touches many files.
- A formatter migration touches everything.
- An external schema change ripples widely.

For these:

- **Pre-discuss** with maintainers. Make sure they expect the size.
- **Note in the PR title**: `[megadiff] formatter migration`.
- **Group changes**: rename in one commit, behavior in another, even if
  squash-merged.
- **Add reviewer guidance**: "Files A, B, C have substantive changes;
  the rest is mechanical."
- **Add a script if possible**: "Generated this with `./scripts/rename.sh`."

A mechanical megadiff is more reviewable than a behavioral megadiff.

## Splitting a Big PR Mid-Stream

You're 1000 lines in. You realize this should be three PRs.

1. **Stop adding.** Don't make it worse.
2. **Identify the first independently-mergeable piece.** Probably the
   foundation (types, scaffolding, refactor).
3. **Extract it.** Use `git rebase -i` or create a new branch from
   `main` and cherry-pick.
4. **Open PR1.** Description says "first of three; #N and #M follow."
5. **Rebase your big branch on PR1's branch.** Continue from there.

This is painful the first time. After you do it twice, it's reflex.

## Splitting Tooling

### `git rebase -i`

Reorder, drop, split, combine commits. Powerful but fiddly.

### `git absorb`

Automatically distributes uncommitted changes into the right past
commits. Magical for keeping a stack of WIP commits clean.

### Tools like `git-spice`, `Graphite`, `git-town`

Help manage PR stacks: rebase one, all downstream get auto-rebased.

## "Reviewable Surface"

Not all lines are equal:

- **High-attention**: business logic, security-sensitive, API contracts.
- **Medium-attention**: refactors, tests.
- **Low-attention**: formatting, generated, lock files.

A PR with 1000 lines of new code is large. A PR with 100 lines of new
code and 1500 lines of regenerated protobuf bindings is small.

## Anti-Patterns

### "Just merge, please"

Asking for sympathy merge on a 2000-line PR. Even when it works,
you're depleting maintainer trust.

### Bundling

"This PR fixes bug A, also B, also adds C, also refactors D." Each
should be separate.

### "I'll split it next time"

You promised that last time. Split now.

### Single-file mega changes

A 1500-line change to one file is just as hard to review as 1500 lines
across many files. Maybe harder.

## Reviewer-Side: Asking for Splits

If you're reviewing a too-large PR:

> "This PR is doing several things — could you split into:
> 1. Refactor (the changes to `service.go`)
> 2. New feature (the new endpoints)
> 3. Tests
>
> Happy to review each quickly once split."

Polite, specific, productive.

## The Counterargument

Occasionally, splitting introduces real costs:

- Coordination overhead (each PR has its own description, CI, comments).
- Stack management complexity.
- The change is genuinely atomic.

For these, bundling is correct. But default to splitting.

## See Also

- [stacked-prs.md](stacked-prs.md)
- [pr-description.md](pr-description.md) — even small PRs need good descriptions
- [../05-fixing-issues/fix-surface-area.md](../05-fixing-issues/fix-surface-area.md) — sizing fixes
