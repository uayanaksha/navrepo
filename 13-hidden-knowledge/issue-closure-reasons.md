# Issue Closure Reasons

An issue getting closed isn't a verdict on you — it's a routing
decision. Each closure reason means something specific and calls for a
different response. Decoding them keeps you from misreading a normal
triage outcome as rejection.

## The Common Closure Reasons

| Reason | Means | Your move |
|---|---|---|
| **Fixed / resolved** | A change addressed it | Verify the fix works for you; thank them |
| **Duplicate** | Already tracked elsewhere | Go to the original; add your info there |
| **By design / works as intended** | Intentional behavior, not a bug | Understand the rationale; reconsider if you disagree |
| **Won't fix / out of scope** | Real, but they won't do it | Accept, fork, or maintain externally |
| **Can't reproduce** | They couldn't see the problem | Provide a better reproduction |
| **Stale / inactive** | No activity for too long | Re-open with fresh info if still relevant |
| **Not planned** | Acknowledged, deprioritized | Make the case, or wait |
| **Invalid / spam** | Not actionable | Re-file properly if it was a real issue |

## Decoding Each

### Fixed / resolved

The good outcome. A commit or release addressed it. Confirm the fix
actually solves *your* case (the fix may differ from what you expected),
and pull the version that contains it.

### Duplicate

Your issue already exists. Not a criticism — issue trackers accumulate
dupes constantly. The original is the canonical thread; add any *new*
detail (a different repro, additional environment) there. Searching
first avoids this (see
[../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)),
but landing on a dupe is harmless.

### By design / works as intended

The behavior you reported is *intentional*. The thing you thought was a
bug is a feature, or a deliberate tradeoff. Before pushing back:

- Read the rationale they give. There's usually a real reason.
- Check whether it's a documented non-goal (see
  [hidden-roadmaps.md](hidden-roadmaps.md)).
- If you still think it's wrong, argue the *use case*, not the
  correctness — "here's a real scenario this breaks." But accept that
  intentional decisions are theirs to make.

### Won't fix / out of scope

They agree it's real but won't address it — it's outside what the
project wants to own. This is the maintainer-calculus verdict (see
[maintainer-calculus.md](maintainer-calculus.md)) and the non-goal
boundary. Options:

- Accept it and adapt.
- Implement it as your own plugin/extension if the architecture allows.
- Fork (last resort — see [notable-forks.md](notable-forks.md)).
- Make a stronger case, *once*, then let it go.

### Can't reproduce

They tried and didn't see the problem. This is the most *fixable*
closure — it usually means your report lacked detail, not that the bug
isn't real. Provide:

- An environment-complete, minimal reproduction (see
  [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md)).
- Exact versions, OS, config.
- A failing test or a repo that demonstrates it.

Then ask to re-open. A great reproduction often flips this immediately.

### Stale / inactive

A bot or maintainer closed it for inactivity. Common on busy projects
with stale-bots. It doesn't mean "no" — it means "nobody moved this in N
days." If it's still relevant, comment with current info and ask to
re-open. Don't take it personally; it's automated hygiene.

### Not planned

Acknowledged as legitimate but deprioritized — they're not saying no,
they're saying not now. The roadmap (and, in company-backed projects,
paying customers — see [open-core-dynamics.md](open-core-dynamics.md))
decides priority. You can make the case for bumping it, offer to
implement it yourself, or wait.

## "Closed" Is Not "Rejected"

The key reframe: most closures are **triage, not judgment**. A healthy
project closes issues constantly — that's the tracker working, not a
sign your contribution was bad. Distinguish:

- **Routing closures** (duplicate, stale, wrong repo): purely
  administrative.
- **Decision closures** (by design, won't fix, not planned): a
  cost/benefit or direction call, not a comment on you.
- **Quality closures** (can't reproduce, invalid): a signal to improve
  the report, not to give up.

None of these mean "you're not welcome." See
[../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)
for the emotional side.

## When You're Closing Issues (As Maintainer)

The flip side — close kindly and clearly:

- **State the reason.** A bare "closed" with no comment frustrates
  reporters. Use the labels/reasons; one sentence of why.
- **Point somewhere.** Dupe → link the original. Won't fix → explain the
  scope boundary. Can't repro → say what you'd need.
- **Thank them.** They spent time filing. Even a declined issue deserves
  "thanks for taking the time."
- **Leave a door open** where appropriate: "happy to reconsider with a
  reproduction." See [saying-no.md](saying-no.md).

## Anti-Patterns

### Reading every closure as rejection

Most closures are administrative. Duplicate and stale say nothing about
your idea's merit. Don't spiral.

### Re-filing a closed-as-duplicate issue

Opening a fresh issue instead of engaging on the original splits the
discussion and annoys maintainers. Go to the canonical thread.

### Arguing with "by design" without a use case

"But it should work the other way" rarely moves a deliberate decision.
"Here's a real scenario it breaks" sometimes does. Lead with the
concrete case.

### Giving up after "can't reproduce"

This closure is an *invitation* to provide a better repro, not a
dismissal. A solid reproduction usually re-opens it.

### Re-opening stale closures with no new info

Just commenting "still relevant?" on a stale-closed issue adds nothing.
Bring current details if you want it re-opened.

## See Also

- [maintainer-calculus.md](maintainer-calculus.md)
- [saying-no.md](saying-no.md)
- [hidden-roadmaps.md](hidden-roadmaps.md)
- [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)
- [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)
