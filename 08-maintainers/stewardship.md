# From Contributor to Maintainer

If you stay around a project long enough and contribute meaningfully,
you may be invited to become a maintainer — or you may want to step
toward it deliberately.

## What Maintainership Means

A maintainer:

- **Merges** PRs (write access).
- **Triages** issues.
- **Sets direction** within their area.
- **Represents** the project externally.
- **Carries** the maintenance burden.

It's a job, even unpaid. People burn out from it.

## The Path

### Stage 1 — Trusted contributor

- Multiple landed PRs.
- Quality, focused work.
- Responsive in review.
- Builds rapport with existing maintainers.

You're not a maintainer yet, but you're known.

### Stage 2 — Reviewer

In some projects:

- You start reviewing others' PRs.
- Maintainers begin to "+1" your reviews as proxy approval.
- You get tagged in CODEOWNERS for areas you know.

This is often informal at first. Some projects formalize it (e.g., a
"maintainer's apprentice" role).

### Stage 3 — Maintainer

Invitation from existing maintainers, usually after:

- A track record of months/years.
- A demonstration of judgment (not just code).
- A commitment to ongoing involvement.

## Stewardship vs Ownership

A subtle distinction:

- **Owner**: someone whose work this is.
- **Steward**: someone responsible for keeping it healthy.

Maintainers are usually stewards, not owners. The project lives
beyond any one person. Acting like an owner ("my project") creates
problems:

- Other contributors feel less ownership.
- Bus factor stays at 1.
- Decisions become idiosyncratic.

Stewards default to:

- Documenting decisions.
- Mentoring contributors.
- Planning for their own succession.
- Not making the project depend on them alone.

## When to Want It

Ask yourself:

- Do I actually use this project?
- Do I want to spend hours per week on it for years?
- Am I OK with the emotional load of "people are angry at my project"?
- Do I have time? (Not "in the abstract" — actually.)

If yes to all: pursue it. If unclear: stay at trusted contributor for
now.

## When to Decline

Sometimes you'll be offered maintainership and shouldn't take it:

- You're already overloaded.
- The project's direction is shifting away from what you care about.
- You can't commit to long-term involvement.

Decline gracefully:

> "Honored to be asked. Right now I can't commit to the long-term
> involvement maintainership deserves. Happy to keep contributing as
> I have been."

Door stays open.

## How to Be a Good Maintainer

Once there, the principles change:

### Be the reviewer you wanted as a contributor

Quick, thoughtful, kind.

### Default to "merge"

Most contributions are good. Don't be the friction.

### Document decisions

When you reject something, explain. When you accept, articulate why.
Future contributors learn from this.

### Triage ruthlessly

Old issues need closing. "Stale bots" aren't kind; clear triage is.

### Stay humble about direction

The project belongs to its users as much as its maintainers. Don't
veto features you personally don't care about if many users want
them.

### Don't burn out

Take breaks. Step back. Recruit co-maintainers. Don't become a single
point of failure.

## Co-Maintenance

Most healthy projects have multiple maintainers:

- Reduces bus factor.
- Spreads review load.
- Provides backup during burnout / vacation.
- Catches blind spots.

Even small projects benefit from a second maintainer.

## Maintainer Politics

The reality of maintenance:

- Disagreements among maintainers happen.
- Sometimes about direction, sometimes about culture.
- Projects sometimes fork because of unresolved disagreements.

Handle:

- Address conflicts early and directly.
- Don't litigate in public unnecessarily.
- Be willing to lose votes.
- Be willing to step away if the project's direction is no longer
  yours.

## When You Inherit

Someone hands you a project. The original maintainer is leaving. Now
what?

- **Read everything** — code, issues, history.
- **Talk to the original** — what they care about, what they didn't
  get to.
- **Don't immediately rebrand** — let the project be itself for a
  while.
- **Communicate the change** in CHANGELOG / README.
- **Set up** for your style (CI, governance, code of conduct as
  needed).
- **Plan for handoff** to your own successor eventually.

Inheriting maintenance is a significant responsibility. Take it
slowly.

## When You Leave

If/when you step back:

- **Announce in advance.**
- **Train co-maintainers.**
- **Reduce bus factor before leaving** (document undocumented
  knowledge).
- **Be available** for a transition period.
- **Don't disappear silently** — community confusion is worse than
  notice.

The kindest leaving is one that didn't surprise anyone.

## Recognizing the Burnout

If your reviews are getting shorter, your patience thinner, your
delight in the work gone:

- Acknowledge it.
- Take time off.
- Ask for help.
- Reduce load (close stale issues, narrow scope).

Don't grind through. Burnout damages the project, not just you.

## See Also

- [postures.md](postures.md) — applies even more as a maintainer
- [../13-hidden-knowledge/governance.md](../13-hidden-knowledge/governance.md) — formal structures
- [burnout-awareness.md](burnout-awareness.md) — recognizing it
- [../14-advanced/onboarding-others.md](../14-advanced/onboarding-others.md)
