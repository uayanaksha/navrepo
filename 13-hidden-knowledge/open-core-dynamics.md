# Open-Core Dynamics

Many "open source" projects are products with a company behind them. The
company's commercial interests shape what gets merged, what's gated
behind a paid tier, and — increasingly — what license the project runs
under. Knowing the business model explains decisions the code never
could.

## Spotting a Company-Backed Project

Tells that a single company controls a project:

- A **CLA** (not just a DCO) that assigns broad rights to one company —
  this is what *enables* a future relicense. See
  [../06-contribution/legal.md](../06-contribution/legal.md).
- Most commits come from `@company.com` addresses.
- An "Enterprise," "Cloud," or "Pro" edition exists.
- The docs funnel toward a hosted/paid offering.
- The trademark and domain are owned by the company, not a foundation.

Contrast with foundation-hosted projects (see
[governance.md](governance.md)), where no single company can
unilaterally change course.

## The Open-Core Model

The common pattern: an open-source *core* plus proprietary *add-ons*.

```
┌─────────────────────────────┐
│   Proprietary / paid tier    │  ← SSO, RBAC, audit logs, scaling,
│   ("Enterprise" / "Cloud")   │     support, compliance features
├─────────────────────────────┤
│      Open-source core        │  ← the thing you're contributing to
└─────────────────────────────┘
```

This isn't inherently bad — it funds full-time maintainers, which is
often *why* the project is healthy. But it has consequences for
contributors.

## What This Means for Your Contributions

### Feature gating

Some features won't be accepted into the open core *because they're the
paid product*. If you contribute SSO, RBAC, or advanced multi-tenancy to
the open tier, you may be building the thing they sell. Expect a polite
decline — not because it's bad, but because it undercuts the business.

This can feel arbitrary until you see the business model. Then it's
obvious: the line between free and paid is a *commercial* decision, not
a technical one.

### Paying customers outrank you

A company-backed project prioritizes the roadmap of its paying
customers. A bug that blocks an enterprise contract gets fixed before
your community-reported one, regardless of technical merit or who filed
first. This is rational for them and frustrating for you. Calibrate
your expectations accordingly.

### Direction serves the business

The roadmap (see [hidden-roadmaps.md](hidden-roadmaps.md)) ultimately
serves revenue. Features that drive adoption or upsell get prioritized;
features that don't, languish — even popular ones.

## License Changes: The Big Risk

The defining risk of company-backed open source: the license can
*change*, usually moving from truly-open to "source-available."

The pattern has repeated across the industry: a company open-sources a
project, builds a community and a business, then — citing cloud
providers monetizing their work without contributing back — relicenses
to something more restrictive.

Common destination licenses:

| License | What it restricts |
|---|---|
| **BSL** (Business Source License) | Source-available; can't offer it as a competing service; converts to open after N years |
| **SSPL** (Server Side Public License) | If you offer it as a service, you must open-source your *entire* service stack — effectively blocks cloud resale |
| **"Fair source" / commons-style** | Various source-available terms, often time-delayed open conversion |

These are **not OSI-approved open-source licenses**. They're
"source-available": you can read and often self-host the code, but
commercial use is restricted. See [license-math.md](license-math.md).

### Why it matters to you

- **Your contributions** to a CLA-assigned project can be relicensed
  *without your consent* — that's what the CLA permits.
- **The fork** that follows a relicense (see
  [notable-forks.md](notable-forks.md)) splits the community and may be
  where the real open-source action moves.
- **Your dependency** on the project may suddenly carry commercial
  restrictions you didn't sign up for. Re-check the license of anything
  business-critical periodically.

## How to Work With It (Not Against It)

- **Know the model before you invest.** Is this a foundation project, a
  community project, or a company product? Your strategy differs.
- **Don't contribute the paid features.** You'll waste your time and
  maybe feel exploited. Build those as your own plugin/extension if the
  architecture allows.
- **Read the CLA.** Understand what rights you're granting. If you're
  uncomfortable, contribute elsewhere.
- **Watch for relicense signals.** Funding pressure, a new CEO, cloud
  competition, VC involvement — these often precede a license change.
- **Accept the customer priority.** Their paying users come first. If
  you need a guarantee, you may need to be a paying customer too.

## It's Not Villainy

Open-core and even relicensing are often *survival* strategies, not
betrayals. Maintaining serious software requires money; "a giant cloud
provider resells our work and we can't pay our maintainers" is a real
problem. Understanding the economics lets you engage clear-eyed instead
of feeling blindsided.

## Anti-Patterns

### Assuming "open source" means community-governed

Plenty of open-source projects are wholly controlled by one company
with commercial goals. Check before assuming neutral governance.

### Contributing the company's paid features for free

Building enterprise SSO into the open core, then being surprised it's
declined. Know where the paywall is.

### Ignoring the CLA's relicense power

Signing a broad CLA and being shocked when the project relicenses. The
CLA *is* the relicense mechanism. Read it.

### Expecting community priority over customers

A company-backed project answers to revenue first. Your free-tier bug
waits behind the enterprise contract. That's the deal.

## See Also

- [license-math.md](license-math.md)
- [notable-forks.md](notable-forks.md)
- [governance.md](governance.md)
- [hidden-roadmaps.md](hidden-roadmaps.md)
- [../06-contribution/legal.md](../06-contribution/legal.md)
