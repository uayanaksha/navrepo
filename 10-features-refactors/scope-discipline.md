# Scope Discipline

The single hardest skill: doing exactly what you said you'd do, no
more.

## What Scope Creep Looks Like

You set out to fix a bug. By PR time:

- The bugfix changed (root-cause shift, fine).
- You "improved" three nearby names.
- You "while you were there" fixed two unrelated bugs.
- You added a feature you've wanted.
- You moved a file because the location bothered you.
- You renamed the formatter config.

This PR is now four PRs in a trench coat. Review will be slow,
risky, and frustrating.

## Why It Happens

- **Efficiency feeling.** "I'm here; might as well."
- **Frustration with surrounding code.** "This is bad; let me fix it."
- **Optimism.** "It's just a few more lines."
- **Lack of upstream tracking.** Without an issue to file the other
  work into, it lands in your branch.

All natural. All disciplined-away with practice.

## The Discipline

For every change you're tempted to add: **does it belong in this PR?**

| Belongs | Doesn't belong |
|---|---|
| Tests for the change | Unrelated improvements |
| Updated docs for the change | Refactors not necessary for the change |
| Refactors strictly required for the change | "While I'm here" cleanups |
| Adjustments to callers of changed code | Wholesale renames |

When in doubt: doesn't belong. Open a separate issue.

## Capturing Out-of-Scope Items

Don't lose the insight; don't put it in the current PR.

### Personal scratchpad

```
TODO (separate PRs):
- Rename `proc_o` to `process_order` (typo-grade, easy PR)
- Extract validation from handler (small refactor PR)
- Investigate why retry policy is 27s (research first)
```

### Open issues immediately

For meaningful items, file an issue right away:

> "Extract OrderValidator from handler. Found while working on #4521;
> not blocker for that fix. Estimated 100-line PR."

Now it's tracked. You won't forget. You haven't bloated the bugfix.

### Comments in code (use sparingly)

```python
# TODO(#4525): refactor to use OrderValidator
```

Only if there's a real follow-up. Otherwise it rots.

## The "It's Trivial" Trap

> "I'll just fix this typo in the comment while I'm here."

Often fine. But know:

- The typo fix's diff is one line; it adds review cost ~zero.
- A rename of `proc_o` to `process_order` is 30 lines. Reviewer
  ratio shifts.
- A refactor of validation is 100 lines. Now the PR is half
  unrelated.

A rule of thumb: < 5 lines of mechanical change, OK. > 5 lines, probably
split.

## "But It'll Take Longer If I Split"

Splitting overhead:
- A second PR.
- A second description.
- Two CI runs.
- Two reviews.

Per-PR overhead is ~30 minutes. Per merged-bundled-PR-with-arguments
overhead can be 30 hours. Split wins overall.

## What Makes a Split Hard

### Dependencies between changes

The refactor enables the fix. Can't ship them separately.

Mitigation:
- PR1: refactor (no behavior change).
- PR2: fix using the new structure.

Each is small. Each is reviewable. PR2 depends on PR1, but the stack
is manageable.

### Interleaved diff

Your changes touch the same lines in incompatible ways.

Mitigation:
- `git rebase -i` to separate commits.
- Or branch from the same base for each PR.

### "While I'm here" momentum

You feel you're "done" once everything is fixed at once. Splitting
feels backwards.

Mitigation:
- Plan splits at the start of work.
- Note "this will be a stack of 3 PRs" up front.

## How to Recognize You've Drifted

Symptoms of scope creep:

- Your PR description's "Changes" bullets keep growing.
- The diff stat grows by hundreds of lines per day.
- You catch yourself making changes you didn't plan.
- You're touching files unrelated to the original task.

Stop. Revert the latest scope creep. Continue on plan.

## Mid-Stream Course Correction

You're 800 lines into a 200-line fix. What to do?

1. **Identify the actual fix.** What lines are necessary for the
   stated goal?
2. **Branch off** at the necessary lines.
3. **Open PR1** with just those.
4. **Continue the rest** as separate PRs (or abandon if not worth).

This costs you a couple hours. Saves you days of rework after rejection.

## "But the Maintainer Asked"

Sometimes a reviewer asks for in-scope additions:

> "Could you also fix the same case in signup.py?"

Two responses:

### Acceptable

> "Sure, including. Updated."

If it's truly trivial and related, fine.

### Better

> "Good catch — I'd prefer to do that in a separate PR for cleaner
> review. Opening #N now and will tag you."

Reviewers usually agree.

For larger additions ("could you also restructure X?"), always split.

## Anti-Patterns

### "Cleanup PR" with no plan

```
chore: cleanup
```

What did you clean? Why now? Often these are scope-creep dumps.

Better: focused improvement PRs with a stated goal.

### "Misc fixes"

PR title says "misc." Description says "various improvements." Five
unrelated things in one PR.

Each should be its own PR with a clear title.

### Refactor-as-side-effect

```
feat: add OAuth PKCE support
- adds PKCE
- refactors entire auth module
- updates logger setup
- renames internal types
```

PKCE is one thing. The rest is three more PRs.

### Drive-by formatting

PR is mostly logic, but includes 200 lines of formatter changes from
running the formatter on extra files.

Separate formatter PRs (or use a `.git-blame-ignore-revs` entry for
the formatter commit).

## When Scope Is Naturally Large

Some changes can't be split:

- A single rename touches many files.
- A schema migration ripples widely.
- An external dependency change cascades.

For these:

- **Be explicit** in the title (`[megadiff]` or similar).
- **Provide a reviewer guide** in the description.
- **Group mechanical from substantive** in commits.
- **Don't add unrelated stuff on top.** Keep the big change focused.

## Self-Check

Before opening a PR, read your own description:

- Does the title describe one thing?
- Do the bullets all relate?
- Could a reviewer summarize this PR in one sentence?

If yes: scope-disciplined.
If no: split.

## See Also

- [../05-fixing-issues/fix-surface-area.md](../05-fixing-issues/fix-surface-area.md)
- [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md)
- [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)
