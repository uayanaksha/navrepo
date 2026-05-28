# Saying No

"No" is the most important word in a maintainer's vocabulary and one of
the hardest for contributors to hear. Knowing how to decline gracefully
— and how to receive a decline — keeps projects focused and
relationships intact.

## Why "No" Is Essential

Every "yes" is a permanent commitment (recall
[maintainer-calculus.md](maintainer-calculus.md)): more surface area,
more maintenance, more support, forever. A project that says yes to
everything becomes a bloated, unmaintainable mess and burns out its
maintainers (see [burnout.md](burnout.md)).

> The health of a project is defined as much by what it *refuses* as by
> what it accepts.

So "no" isn't hostility — it's curation. The maintainer protecting scope
is doing their job.

## Saying No as a Maintainer

The art is declining the *change* while keeping the *contributor*. A
good "no" is clear, kind, reasoned, and timely.

### The structure of a good no

1. **Thank them.** They spent real effort.
2. **Be clear it's a no.** Don't leave false hope with vague hedging.
3. **Give the reason.** Scope, direction, maintenance cost,
   compatibility — the actual calculus.
4. **Leave a door where one exists.** An alternative, a plugin path, a
   "reconsider if X."

### The "happy to close if this doesn't fit" pattern

A graceful, low-pressure formula — useful from *either* side:

> "This is a reasonable idea, but it adds surface area we don't want to
> maintain in core. I'd suggest it as a plugin. Happy to close this if
> that doesn't work for you — and thanks for the contribution."

It's honest, it's kind, it offers a path, and it doesn't leave the PR
rotting in limbo. (A contributor can use the same phrasing to
gracefully withdraw: "Happy to close this if it doesn't fit your
direction.")

### Common honest reasons (state the real one)

| Reason | Phrasing |
|---|---|
| Out of scope | "This is outside what we want core to do." |
| Too much maintenance | "We can't take on maintaining this long-term." |
| Wrong direction | "We're actually moving away from this approach." |
| Adds surface area | "We're trying to keep the API small." |
| Better as a plugin | "This fits better as an extension than in core." |
| Not now | "Good idea, but not a priority this cycle." |

## The Cost of *Not* Saying No

Avoiding "no" is worse than saying it:

- **Silence** leaves contributors hanging for months, then feeling
  ghosted — more hurtful than a prompt, kind decline. Across time zones
  this is especially corrosive (see [time-zones.md](time-zones.md)).
- **Vague maybes** ("interesting, we'll see") create false hope and a
  PR that lingers forever.
- **Yes-by-exhaustion** — merging just to end the discussion — bloats the
  project and trains contributors that persistence beats merit.

A fast, kind "no" respects everyone's time more than a slow, soft
non-answer.

## Receiving a No as a Contributor

When *you're* told no, the graceful response protects the relationship
and your reputation:

- **Accept it without relitigating.** You can make your case *once*
  (see [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)),
  but past that, pushing erodes goodwill.
- **Assume the calculus, not malice.** "No" almost always reflects cost
  or direction, not a judgment of you or your code (see
  [issue-closure-reasons.md](issue-closure-reasons.md)).
- **Take the offered path.** If they suggest a plugin or a different
  approach, that's a real opening.
- **Thank them and move on.** "Makes sense, thanks for considering it"
  leaves the door open for next time.
- **Keep your options.** You can maintain a fork or plugin if you truly
  need the feature — without resentment (see
  [notable-forks.md](notable-forks.md)).

How you take a "no" is remembered as much as the quality of your code.
The contributor who accepts declines gracefully gets their *next*
proposal taken seriously.

## Saying No to Scope Creep (Yours and Theirs)

"No" also operates within your own work:

- **To yourself:** declining the "while I'm here" temptation (see
  [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)).
- **To a reviewer's scope expansion:** "I'd rather do that in a separate
  PR" is a graceful no to scope creep (see
  [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)).

Same skill, smaller scale: protect the boundary, kindly.

## Anti-Patterns

### Ghosting instead of declining

Letting a PR sit silently because saying no is uncomfortable. Silence is
crueler than a clear, kind decline. Close it with a reason.

### The cruel no

A dismissive or contemptuous decline ("obviously we'd never do this")
costs you a contributor and your reputation. Decline the change, respect
the person.

### Relitigating a no (as contributor)

Arguing past the maintainer's clear decision, re-opening, pinging,
re-proposing. Make your case once, then accept it. Persistence here
backfires.

### Yes-by-exhaustion

Merging something you don't want just to end the conversation. It bloats
the project and rewards the wrong behavior. Hold the line, kindly.

### Hedged non-answers

"Maybe, we'll see, interesting…" that never resolves. Decide and say so;
false hope wastes more time than honesty.

## See Also

- [maintainer-calculus.md](maintainer-calculus.md)
- [issue-closure-reasons.md](issue-closure-reasons.md)
- [burnout.md](burnout.md)
- [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)
- [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)
