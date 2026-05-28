# No Drive-Bys

A drive-by is an unrelated change you slip into a PR because you
happened to be in the file. It feels helpful. It's usually a tax on
your reviewer and a risk to your change. This is the *mindset* behind
the mechanics in
[../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md).

## What Counts as a Drive-By

You're fixing a bug in `orders.py` and you notice:

- A typo in a nearby comment.
- A variable name you'd have chosen differently.
- A function that "should really" be refactored.
- An unrelated bug two functions down.
- Inconsistent formatting the linter didn't catch.

Each is real. Each is tempting. Each, added to your bugfix PR, makes the
change harder to review, harder to revert, and harder to bisect later.

## Why the Urge Is Strong (and Why to Resist)

The "while I'm here" feeling is genuine efficiency instinct — you've
paid the cost of loading this file into your head, so doing more now
seems free. But:

- **Review cost isn't free.** Every extra concern in the diff is
  another thing your reviewer must understand and verify. A focused
  20-line fix gets a fast approval; the same fix plus three drive-bys
  gets a slow, hesitant one.
- **Revert cost isn't free.** If the bugfix needs reverting, your
  unrelated cleanup goes with it — or someone has to untangle them.
- **Bisect cost isn't free.** When a regression is traced to your
  commit, the next person has to figure out *which* of your five
  changes caused it.
- **Blast radius isn't free.** The refactor you tossed in "while you
  were there" is exactly the kind of change that breaks something
  subtle and unrelated to the bug you were fixing.

A clean diff is a gift to everyone downstream, including future-you.

## The Discipline: Park, Don't Drop

The skill isn't *ignoring* the things you notice — your eye for them is
valuable. The skill is *capturing them without acting now*:

- **A quick note.** A scratchpad of "things to come back to."
- **An issue.** For anything meaningful: file it immediately, with the
  context fresh. "Noticed while fixing #4521; not a blocker."
- **A follow-up PR.** Land the focused fix, then open a separate PR for
  the cleanup. Stack them if they're related.

You lose nothing. You've recorded the insight; you just haven't
contaminated the current change with it.

## The Exception: Truly Trivial, Truly Adjacent

A one-character typo fix on the exact line you're already changing? Fine.
The rule of thumb: if it's **on the lines you're already touching** and
**under a handful of characters**, it adds ~zero review cost. The moment
it's a separate region of the file or more than a line or two, it's a
drive-by — park it.

## When Someone Asks You to Add Scope

A reviewer says "while you're at it, could you also fix X?" Two good
responses, depending on size:

- **Trivial and related:** "Sure — done."
- **Anything bigger:** "Good catch — I'd rather do that in a separate
  PR so this one stays reviewable. Filed #N, tagging you."

Reviewers almost always agree. You're not refusing the work; you're
sequencing it cleanly.

## The Cultural Signal

Consistently focused PRs build a reputation: maintainers learn that
*your* changes do one thing, are easy to review, and are safe to merge.
That reputation is the currency that gets your later, bigger changes
trusted. Drive-bys spend that currency for the price of a typo fix.

## Anti-Patterns

### The "while I'm here" cascade

One small cleanup invites the next, and an afternoon later your 20-line
fix is a 400-line grab-bag. Notice the first one and stop.

### The cleanup PR with no boundary

"chore: cleanup" that touches fifteen unrelated things is just a bag of
drive-bys with a title. If you're doing cleanup, scope *that* too — one
kind of cleanup per PR.

### Refactoring in a bugfix

The most dangerous combination: a behavior change (the fix) hidden
inside a structure change (the refactor). If it breaks, no one can tell
which half did it. Separate them.

### Hoarding the insights instead of filing them

The opposite failure: noticing real problems and never recording them,
so they're lost. Park them in an issue — the discipline is *defer*, not
*discard*.

## See Also

- [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)
- [../05-fixing-issues/fix-surface-area.md](../05-fixing-issues/fix-surface-area.md)
- [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md)
- [be-the-contributor.md](be-the-contributor.md)
