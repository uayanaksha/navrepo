# Incident Response

When production is broken, the skills that matter shift from "write good
code" to "restore service calmly, communicate clearly, and learn
without blame." Incidents are inevitable; handling them well is
learnable.

## The First Priority: Stop the Bleeding

During an incident, **restoring service beats finding the root cause.**
These are different activities, and conflating them prolongs outages:

- **Mitigate first.** Roll back, fail over, disable the feature flag,
  scale up, drain the bad node — whatever makes users whole *now*.
- **Diagnose later.** The satisfying hunt for *why* can happen once
  users are unblocked. Debugging a live outage while users suffer is the
  wrong order.

> Rollback is almost always faster than roll-forward. If a recent deploy
> correlates with the incident, revert it first and investigate after —
> don't try to fix-forward under pressure.

This is *why* the practices in
[../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)
and [../10-features-refactors/feature-flags.md](../10-features-refactors/feature-flags.md)
matter: they give you fast mitigation levers.

## Roles: Who Does What

Even a small incident benefits from explicit roles, so things don't fall
through the cracks or get done five times:

| Role | Responsibility |
|---|---|
| **Incident Commander (IC)** | Coordinates, decides, owns the response — *not* necessarily the one debugging |
| **Operations / responders** | The hands doing the technical mitigation |
| **Communications** | Updates stakeholders/status page so responders aren't interrupted |
| **Scribe** | Records the timeline as it happens (invaluable for the postmortem) |

The key insight: **the IC coordinates, they don't fix.** Separating
"who's deciding and tracking" from "who's typing commands" keeps a
chaotic situation organized. In a small incident one person may wear
several hats, but the *functions* still need covering.

## The War Room

Concentrate the response in one place (a call, a dedicated chat
channel):

- **One source of truth** for what's known and what's being tried.
- **Avoid the "too many cooks" failure** — uncoordinated responders
  making simultaneous changes can make things worse and obscure cause
  and effect. The IC gates risky actions.
- **Log actions as you take them** ("rolling back deploy X at 14:32") —
  both to coordinate and to feed the postmortem timeline.
- **Change one thing at a time** where possible, so you can tell what
  helped — incident debugging is still the scientific method (see
  [../14-advanced/debugging-toolkit-deep-dive.md](../14-advanced/debugging-toolkit-deep-dive.md)),
  just under time pressure.

## Communication During an Incident

Communication is a first-class task, not a distraction from the "real"
work:

- **Update on a cadence.** Regular updates ("still investigating, next
  update in 30 min") even with no news — silence breeds panic and a
  flood of "is it fixed yet?" interruptions.
- **Separate audiences.** Technical detail in the war room; plain-
  language impact for stakeholders and users ("some users can't log in;
  we're working on it").
- **Be honest and concrete** about impact and ETA — or honest that you
  don't have an ETA yet. Don't over-promise a fix time under pressure.
- **Designate one communicator** so responders aren't pulled away to
  answer questions.

## Runbooks

A runbook is a pre-written guide for handling a known scenario — written
*calmly in advance* so you don't have to improvise at 3 a.m.:

- **Steps to diagnose and mitigate** a specific failure mode.
- **Where the dashboards, logs, and levers are.**
- **Who to escalate to** and how.

Good runbooks turn a panicked improvisation into a checklist. After each
incident, the actions you wished you'd had written down become the next
runbook. They're also a key onboarding artifact for on-call (see
[onboarding-others.md](onboarding-others.md)).

## Blameless Postmortems

After the incident, write a postmortem — and make it **blameless**. This
is the single most important cultural practice in incident response.

### Why blameless

If people are punished for incidents, they hide information, and you
lose the ability to learn. Blameless culture assumes everyone acted
reasonably given what they knew, and asks **"what about our *system*
allowed this?"** not "who screwed up?"

- "An engineer ran a bad migration" → blame, learning stops.
- "Our process let an unreviewed migration reach prod with no dry-run
  or rollback plan" → systemic, actionable, learning continues.

The human who "caused" it is almost never the root cause; the system
that *allowed* a single human action to cause an outage is.

### What a postmortem contains

- **Timeline.** What happened, when (from the scribe's log).
- **Impact.** Who was affected, how badly, for how long.
- **Root cause(s).** The systemic *why*, dug into properly (the five
  whys — see [../05-fixing-issues/five-whys.md](../05-fixing-issues/five-whys.md)).
- **What went well / poorly.** Including the response itself.
- **Action items.** Specific, *owned*, with deadlines — to prevent
  recurrence and improve response.

### Action items are the point

A postmortem with no follow-through is theater. Each action item needs
an owner and a due date, and someone must track them to completion. The
goal isn't a document; it's a system that won't fail the same way twice.

## Learn From Incidents Generally

Reading *others'* postmortems is one of the highest-leverage learning
habits (see [../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md))
— you absorb failure modes without living through them. Many of those
failure modes are the distributed-systems ones in
[working-in-distributed-systems.md](working-in-distributed-systems.md):
retry storms, cascading failures, capacity exhaustion.

## Anti-Patterns

### Debugging while users suffer

Hunting the root cause while the outage continues. Mitigate first (roll
back, fail over), diagnose after.

### Fixing forward under pressure

Trying to patch and redeploy in the heat of an incident instead of
reverting to a known-good state. Roll back; fix forward calmly later.

### No coordination (too many cooks)

Multiple people making simultaneous uncoordinated changes, making it
worse and obscuring cause. Designate an IC; change one thing at a time.

### Communication blackout

Going silent during an incident. Stakeholders panic and flood
responders. Update on a cadence, even with no news.

### Blameful postmortems

Hunting for who to punish. People hide information, learning stops, and
the same failure recurs. Blame the system, not the human.

### Postmortems with no follow-through

A document full of action items nobody owns or tracks. The action items
*are* the value; assign and complete them.

## See Also

- [working-in-distributed-systems.md](working-in-distributed-systems.md)
- [../05-fixing-issues/five-whys.md](../05-fixing-issues/five-whys.md)
- [../10-features-refactors/feature-flags.md](../10-features-refactors/feature-flags.md)
- [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)
- [../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md)
