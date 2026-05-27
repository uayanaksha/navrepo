# Fix Surface Area

How much code your fix changes — the **surface area** — is itself a
design decision. Smaller isn't always best. But "small" should be your
default.

## Calibration Table

| Bug type | Appropriate surface |
|---|---|
| Typo, off-by-one | 1–3 lines |
| Logic error in a single function | 1–20 lines |
| Wrong shape of returned data | A function and its callers' typing |
| Bug in a shared utility | The utility + tests; callers unchanged |
| Bug caused by a missing abstraction | Add the abstraction; refactor callers |
| Bug caused by *bad* abstraction | **Don't refactor in the bugfix.** Patch; refactor separately |
| Class of bugs (security, perf) | Possibly large; consider splitting |

The rightmost column is a starting point. Adjust based on context.

## Why Smaller Is Default

A small fix:

- Reviews faster.
- Is easier to verify.
- Has lower regression risk.
- Doesn't bundle unrelated decisions.
- Merges sooner.

Reviewers can hold ~200-400 lines in their head before quality drops.
Above 600, real review stops happening.

## When To Go Bigger

There are cases where a larger fix is correct:

### The same bug exists in 12 places

If the same root cause produces 12 symptoms, fixing all 12 in one PR is
often clearer than 12 PRs. Especially if the fix is mechanical (e.g., "add
input sanitization to every endpoint").

### The fix requires a refactor

Sometimes the bug exists *because* the code is structured wrong. A small
fix would be incorrect or defensive. A modest refactor *is* the fix.

But be careful: confirm the refactor is in-scope. Most of the time,
"refactor while we're here" is scope creep.

### The fix is part of a tightly-coupled change

Some fixes can't land alone (e.g., changing both API and a client).
Land them together to keep main always-working.

## What NOT To Include

Even when fixing a bug, don't bundle:

- **Unrelated bug fixes you noticed.** File separately.
- **Refactors you wanted to do anyway.** File separately.
- **"Improvements" while you're here.** Naming, formatting,
  reorganization. File separately.
- **Cleanups of comments / docs not directly relevant.** File
  separately.
- **Dependency upgrades.** Definitely separately.

When you find yourself adding bullets to the PR description that say
"also fixed X" — those should be separate PRs.

## "While I'm Here" — The Trap

Spotting an unrelated improvement while in a file is normal. The
discipline is:

1. **Open a separate issue** or note in your scratchpad.
2. **Leave the bug fix focused.**
3. **Come back later** if motivated.

This is the single hardest skill in this manual. It feels efficient
to bundle. It isn't.

## Splitting a Big Fix

When a fix is necessarily large (e.g., 1000-line refactor required):

### Stack the PRs

PR1: Add new abstraction (no behavior change).
PR2: Migrate callers to new abstraction (no behavior change).
PR3: Use new abstraction to fix the bug.

Each PR is reviewable. Each can be reverted independently.

### Land scaffolding first

A 1500-line refactor that adds new types is split as:

- PR1: introduce new types alongside old.
- PR2: convert one call site.
- PR3: convert remaining call sites.
- PR4: remove old types.

This pattern is called "expand-contract" or "two-step migration."

### Feature-flag the change

For risky refactors, gate the new path behind a flag. Default off,
test on, gradually flip.

See [../10-features-refactors/feature-flags.md](../10-features-refactors/feature-flags.md).

## When To Just Bundle

For truly tightly-coupled changes that aren't separable, bundle is
fine. Examples:

- A protocol change requiring server and client update simultaneously.
- A type rename that requires updating all call sites at once.
- A test that depends on a fix.

Just be explicit in the PR description about *why* bundling is correct.

## The Diff Smell Test

Before opening a PR, look at your diff:

```bash
git diff --stat main...HEAD
```

If you see:
- Files changed > 10 → probably should split.
- Lines changed > 500 → probably should split.
- Many small unrelated edits → split for sure.

These aren't absolutes; context matters. But they're worth pausing on.

## Splitting In Progress

If you discover mid-implementation that your "bug fix" became three
PRs' worth of work:

1. Stop.
2. `git stash` or branch off.
3. Open the smallest PR that's still useful.
4. Continue the rest separately.

Don't sunk-cost into shipping a giant PR you've already invested in.

## A Worked Example

**Symptom:** Login fails when email has uppercase.

**Naive surface:**
```
- Add `.lower()` in login handler. 1 line.
```

**Better surface:**
```
- Add `.lower()` in `find_user_by_email()`. 1 line.
- Test for the case-sensitive scenario. 5 lines.
- Total: 6 lines.
```

**Scope-creep surface:**
```
- Same as above. 6 lines.
- Plus: refactor email handling into a UserEmail type. 200 lines.
- Plus: add email validation to signup. 50 lines.
- Plus: migrate existing emails to be lowercase in DB. 80 lines.
- Total: 336 lines.
```

The right answer is the middle. The migration and refactor are real,
related concerns — but they belong in separate PRs with their own
discussions.

## Talking About Scope With Reviewers

If a reviewer asks for scope changes:

**Reviewer**: "Could you also fix the same issue in `signup.py`?"

**You** (good response): "Good catch. I'd prefer to do that in a
separate PR for cleaner review — opening #N now."

**You** (acceptable response): "Sure, including. Now also affects
signup.py."

The former keeps PRs small. The latter is fine when the additional
change is truly trivial.

## The "Tiny PR Series" Strategy

For some projects, a series of 5 tiny PRs each landing in a day beats
one 5-day PR. Pros:

- Faster review.
- Less merge conflict risk.
- Easier rollback.
- Continuous integration.

Cons:

- More overhead per PR (description, CI run).
- Reviewer fatigue if poorly stacked.

Match the project's preference.

## See Also

- [root-cause-vs-symptom.md](root-cause-vs-symptom.md) — informs the surface
- [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md) — splitting in the PR context
- [../07-pull-requests/stacked-prs.md](../07-pull-requests/stacked-prs.md)
