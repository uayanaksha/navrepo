# Long-Running PRs

A PR that takes longer than a week is at risk. After a month, it's
fighting entropy. The skill is keeping it alive without exhausting
reviewers or yourself.

## Why PRs Decay

- **Main moves.** Conflicts accumulate.
- **Reviewers shift focus.** Context lost.
- **Your own context fades.** What was in your head two weeks ago is
  gone.
- **The change loses relevance.** Other work may have made it
  unnecessary.

The longer it sits, the harder to merge.

## Anti-Decay Habits

### Sync with main weekly

```bash
git fetch upstream
git rebase upstream/main   # or merge, per project convention
git push --force-with-lease
```

Don't let it drift more than a week.

### Respond to comments within days

Even a "Looking into it, will reply within Friday" is better than
silence. Maintainers tolerate slow; they don't tolerate ghost.

### Summarize what changed between pushes

When you push fixes after review feedback:

```markdown
## Update 2026-04-12

Responded to all comments. Substantive changes:
- Renamed `processOrder` to `placeOrder` (Alice's suggestion).
- Added test for the empty-cart case (Bob's suggestion).
- Reverted the unrelated formatting changes (out of scope).

Open questions for reviewers:
- Bob asked about the retry semantics — replied inline.
```

Reviewers can re-engage in 2 minutes instead of 20.

### Keep the PR description current

Update the "Status" or top-level description as scope shifts:

```markdown
## Status

In review. Addressed all feedback as of 2026-04-12. Waiting on Alice
for final approval on the API shape.
```

A maintainer skimming PR lists sees this.

## Communicating Slowdowns

If you can't actively push for a while:

> "Heads up — out for a week starting Friday. Will pick back up
> Monday the 22nd. No need to wait for me on review — happy to
> address everything when I return."

Set expectations. Reviewers can plan.

## Knowing When to Close

Some PRs aren't going to make it. Signs:

- 6+ weeks open with no maintainer engagement after multiple pings.
- Direction has shifted in main; PR no longer applies.
- Disagreement between you and maintainer that hasn't resolved.
- You no longer have time / interest.

Closing gracefully:

> "Going to close this — doesn't seem to fit the project's direction
> right now. No hard feelings; can reopen anytime if it's useful."

Closing isn't failure. Stale PRs hurt the issue tracker; closing helps.

## Reopening Closed PRs

If you closed and now have reason to revisit:

> "Reopening — the original concern was about X; with #N landed, that's
> no longer relevant. Rebased and ready for fresh review."

Maintainers appreciate this framing — you've addressed the original
blocker.

## When the Reviewer Is the Problem

Sometimes reviewer slowness is the issue:

- Project has too few reviewers.
- Your assigned reviewer has been out.
- Reviewer didn't realize you're waiting.

Tactics:

- **Polite ping after 2 weeks.**
- **Ask a different maintainer** if multiple are listed:
  > "Hi — Alice has been busy. Would you be available to look at this
  > when convenient?"
- **Mention in a public channel** (Discord, Slack) if the project has
  one.
- **Suggest unassigning yourself** as a last resort: "Should we close
  this and revisit when there's bandwidth?"

Never aggressive. Always polite escalation.

## Rebase vs Merge for Updates

For long-running PRs, the rebase-vs-merge question becomes more acute:

### Rebase

- Clean linear history.
- Force-push required.
- Reviewer inline comments may be lost.

### Merge

- Preserves history.
- No force-push.
- "Merge commits" clutter the branch but reviewer context preserved.

For a long-running PR, **merge** is often better — preserves reviewer
work. Squash at the end (if project squash-merges).

## Conflict Resolution

Conflicts on a long-running branch:

```bash
git rebase main
# or
git merge main

# resolve conflicts in your editor
git add <resolved-files>
git rebase --continue
# or
git merge --continue
```

After resolving, **test**. Conflict resolution often introduces subtle
bugs.

Communicate in the PR:

> "Rebased on main; resolved conflicts in `service/orders.go` (where
> Alice's PR #1230 touched the same lines). Re-ran tests; all pass."

## Splitting a Long-Running PR

If review is stuck because the PR is too big:

1. **Identify the controversial piece.** Often most discussion is
   about one part.
2. **Land the uncontroversial pieces** as separate PRs first.
3. **Continue arguing about the rest** with less rebase pressure.

This is the most reliable way to unstick a sprawling PR.

## When the Project Has Stalled

If the whole project hasn't merged anything in months, your PR's
slowness isn't personal. Calibrate expectations.

Options:

- Wait it out.
- Fork and use your own version.
- Move on to another project.

A long-stalled PR isn't worth multiple months of your time.

## Letting It Go

Sometimes a PR you cared about isn't going to merge. It's OK to:

- Close it.
- Keep your fork with it applied.
- Note publicly: "Not merged upstream; using a fork."

Open source is voluntary. You don't owe a maintainer infinite
revision.

## Old PR as Reference

A closed PR isn't deleted. Future contributors searching for similar
work find it. Closing with explanation helps them:

> "Closing — approach was rejected because of Y. Alternative
> implementation now in #N."

You're contributing to future searchability.

## See Also

- [pr-description.md](pr-description.md) — keep it current
- [../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md)
- [stacked-prs.md](stacked-prs.md) — splitting strategies
