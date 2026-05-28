# Burnout

Maintainer burnout is the quiet systemic risk under most open source.
The deeper, structural view of *why* it happens — and how to read it in
a project — helps you contribute in ways that don't accelerate it.
(See [../08-maintainers/burnout-awareness.md](../08-maintainers/burnout-awareness.md)
for the day-to-day interaction side.)

## The Structural Problem

Open source's economics are quietly brutal:

- A handful of unpaid people maintain software that millions — and
  whole companies — depend on for free.
- Demand scales with popularity; maintainer capacity doesn't.
- The work is largely thankless: bug reports, entitled demands, and
  "any update?" pings vastly outnumber thank-yous.
- Saying no is emotionally costly; saying yes is unsustainable.

The famous mental image: vast modern infrastructure resting on a single
component "some random person in Nebraska has been thanklessly
maintaining since 2003." When that person burns out, the consequences
ripple across everything downstream.

## Why Maintainers Burn Out

| Driver | Mechanism |
|---|---|
| **Volume** | Issues and PRs arrive faster than one person can process |
| **Entitlement** | Users treating free software like a paid support contract |
| **No off switch** | Notifications never stop; the backlog is never empty |
| **Emotional labor** | Saying no, mediating disputes, absorbing hostility |
| **Isolation** | Often a single maintainer with no one to share the load |
| **Thanklessness** | Complaints are loud; gratitude is rare |
| **Identity fusion** | The project becomes their identity; stepping back feels like failure |

It's rarely the *coding* that burns people out. It's the unbounded
social and emotional load around the coding.

## Reading Burnout in a Project

Before contributing (or depending), you can often see the warning
signs:

- **Slowing response times** trending worse over months.
- **Growing backlog** — open issues/PRs climbing, few closing.
- **Bus factor of one** — all recent commits from a single person.
- **Terse or frustrated tone** creeping into maintainer replies.
- **Explicit signals** — a maintainer posting "I'm overwhelmed," reducing
  scope, or asking for help in the README/issues.
- **Stale-bot reliance** — auto-closing issues because there's no human
  capacity to triage.
- **"Looking for maintainers"** notes — the loudest signal.

```bash
# Is this a one-person project? (bus factor)
git shortlog -sn --since='1 year ago' | head

# Is activity declining?
git log --since='2 years ago' --pretty='%ad' --date=format:'%Y-%m' \
  | sort | uniq -c
```

A project with one exhausted maintainer is a different contribution
environment than a healthy multi-maintainer one — and a riskier
dependency.

## How Not to Accelerate It

Every interaction either adds load or reduces it. Be in the second
category:

- **Reduce, don't add, work.** A PR with tests, docs, and a clear
  description is *less* work to merge than the bug report alone was. A
  vague demand is pure load. Be the contributor who lightens the queue
  (see [../12-mindset/be-the-contributor.md](../12-mindset/be-the-contributor.md)).
- **Don't ping impatiently.** Each "any update?" is a small tax. One
  polite follow-up after a real interval (see [time-zones.md](time-zones.md)).
- **Accept "no" and "not now" gracefully.** Arguing a declined issue
  extracts emotional labor. See [issue-closure-reasons.md](issue-closure-reasons.md).
- **Say thank you.** Genuinely rare, genuinely restorative. Costs
  nothing.
- **Triage for them.** Reproducing others' bugs, answering questions,
  reviewing PRs — this *removes* load and is how you become trusted.
- **Never treat them as a support desk.** They owe you nothing; they're
  giving you software for free.

## When You're the Maintainer

If you maintain something, burnout-proofing is a real skill:

- **Set boundaries early.** Document scope and non-goals so "no" is
  pre-stated, not a per-issue battle. See [saying-no.md](saying-no.md).
- **Automate the toil.** CI, linters, templates, bots — let machines do
  the repetitive triage.
- **Grow the bus factor.** Mentor co-maintainers *before* you're
  drowning, not after. See [../14-advanced/onboarding-others.md](../14-advanced/onboarding-others.md).
- **Permit yourself to step back.** A project going dormant or handing
  off is healthier than a maintainer breaking. Stepping away isn't
  failure.
- **Get funded if you can.** Sponsorships, grants, or a foundation home
  change the sustainability math.

## The Sustainability Movement

The ecosystem has responded with funding and structural mechanisms —
sponsorship platforms, foundations that employ maintainers, grants, and
corporate programs that pay people to maintain critical dependencies.
None fully solves it, but the recognition that "free" software has real
human costs is now mainstream. As a contributor at a company that
depends on open source, *advocating to fund your critical dependencies*
is one of the highest-leverage things you can do.

## Anti-Patterns

### Treating maintainers as a paid support team

Demands, deadlines, and entitlement aimed at volunteers. The single
biggest accelerant of burnout. They chose to share; they didn't sign a
support contract.

### Adding load without removing any

Filing vague issues, pinging for status, arguing closures — all pure
tax. Aim to *net reduce* the maintainer's work with each interaction.

### Depending on a one-person project blindly

Critical infrastructure resting on a single burning-out maintainer is a
risk to *you* too. Know the bus factor; consider funding or
contributing to spread the load.

### Ignoring the signs and piling on

A visibly overwhelmed maintainer doesn't need your feature request
escalated. Read the room; offer help, not more demands.

## See Also

- [../08-maintainers/burnout-awareness.md](../08-maintainers/burnout-awareness.md)
- [time-zones.md](time-zones.md)
- [issue-closure-reasons.md](issue-closure-reasons.md)
- [saying-no.md](saying-no.md)
- [../12-mindset/be-the-contributor.md](../12-mindset/be-the-contributor.md)
