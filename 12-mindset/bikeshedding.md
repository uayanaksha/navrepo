# Bikeshedding

Bikeshedding is the tendency to spend disproportionate time on trivial,
easy-to-grasp details while the hard, important decisions get a fraction
of the attention. Learn to spot it, escape it, and not start it.

## The Origin

C. Northcote Parkinson's "law of triviality": a committee asked to
approve a nuclear power plant waves through the reactor (too complex to
argue about) but spends an hour on the design of the bike shed — because
*everyone* has an opinion on a bike shed.

In code review, the bike shed is:

- The name of a variable.
- Tabs vs. spaces, line length, brace style.
- The exact wording of a log message.
- Whether a helper goes in `utils.py` or `helpers.py`.

The reactor is:

- The concurrency model.
- The data migration's safety.
- The API contract you'll be stuck with for years.

The tragedy: the trivial things get the argument *because* they're easy
to have opinions on, while the consequential things get a quick LGTM
*because* engaging with them is hard.

## Why It Happens

- **Low barrier to opinion.** Everyone can weigh in on a name; few feel
  qualified to challenge the locking strategy.
- **It feels productive.** You're commenting, engaging, "improving"
  things. Activity, not progress (see
  [activity-vs-progress.md](activity-vs-progress.md)).
- **It's safe.** Arguing about formatting risks nothing. Challenging the
  architecture risks being wrong in public.

## How to Recognize It

You're probably bikeshedding when:

- The thread is long and the subject is cosmetic.
- The same trivial point is being re-litigated by multiple people.
- The decision is genuinely reversible in thirty seconds later.
- You feel strongly about something that, honestly, doesn't matter.

A good gut check: **"Will anyone remember this decision in a month?"**
If not, it's a bike shed.

## How to Escape It

### Automate the trivial away

The cleanest fix: remove the *opportunity* to bikeshed. Formatting,
import order, line length — hand them to a formatter (`prettier`,
`black`, `gofmt`, `rustfmt`) and a linter. There's nothing to argue
about when the tool decides and everyone runs it on save. See
[../11-tooling/local-ci.md](../11-tooling/local-ci.md).

### Name the dynamic, gently

Sometimes just saying it dissolves it:

> "I think we're bikeshedding the name here — any of these work. Let's
> pick `parseConfig` and move on to the retry logic, which I think
> actually needs eyes."

This both ends the trivial thread and redirects energy to the reactor.

### Defer with "non-blocking"

If you have a genuine-but-minor preference, mark it so it can't stall
the change:

> "nit (non-blocking): I'd call this `count` not `n`, but up to you."

The author can take it or leave it; the PR isn't held hostage to it.

### Timebox and default

For team decisions that genuinely don't matter much: pick a default,
set a short deadline, move on. "Going with option A unless someone
objects by EOD." Reversible, low-stakes decisions don't deserve
consensus rituals.

## Don't Be the Bikeshedder

Before leaving a comment on something cosmetic, ask:

- Is this a real problem or just *different from how I'd do it*?
- Is it already handled by the formatter/linter? (Then it's a config
  question, not a review comment.)
- Would I block the merge over this? If no, mark it non-blocking — or
  don't say it at all.

Personal taste dressed as a standard is the most common form of
bikeshedding. "I prefer" is not "this is wrong."

## Don't Be Bikeshed-able

You can also reduce the *surface* others bikeshed on:

- **Run the formatter and linter before pushing**, so there's nothing
  cosmetic to comment on.
- **Match the surrounding style** so naming/structure doesn't stand out.
- **Pre-empt the obvious nits** in your PR description ("naming follows
  the existing `fooBar` convention in this module").

The fewer trivial footholds you leave, the faster review focuses on
what matters.

## When the "Trivial" Thing Isn't

A caution: some apparently-cosmetic things genuinely matter.

- A *public* API name is forever; bikeshed-grade attention is
  warranted because it's not reversible.
- A misleading name causes real bugs; that's a correctness issue
  wearing a cosmetic costume.
- Consistency in a large codebase has compounding value.

The test is reversibility and reach: a private local variable is a bike
shed; an exported function in a library used by thousands is a reactor
with a small label.

## Anti-Patterns

### Winning the bike shed, losing the reactor

You spent your review energy and credibility on the variable name and
had nothing left to notice the race condition three lines down. Spend
attention where the risk is.

### Formatting wars in review

Any review thread about whitespace is a sign the project lacks an
enforced formatter. Fix it at the tooling layer, permanently.

### "Strong opinions" on everything

If every detail is a hill, people stop listening to you on the hills
that matter. Reserve strong positions for consequential, hard-to-reverse
decisions.

## See Also

- [activity-vs-progress.md](activity-vs-progress.md)
- [receiving-review.md](receiving-review.md)
- [../11-tooling/local-ci.md](../11-tooling/local-ci.md)
- [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)
