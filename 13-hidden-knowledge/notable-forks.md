# Notable Forks

A fork is open source's ultimate check on power: if a community
disagrees strongly enough with where a project is going, it can take the
code and leave. The famous forks are case studies in governance,
licensing, and what makes communities split — worth knowing for the
lessons, not the trivia.

## Why Forks Happen

The license *guarantees* the right to fork (that's the point of open
source), but communities only exercise it under real pressure:

- **License change.** A company relicenses to source-available; the
  community forks to preserve a truly-open version. (See
  [open-core-dynamics.md](open-core-dynamics.md),
  [license-math.md](license-math.md).)
- **Governance breakdown.** Contributors lose faith in how decisions are
  made and split to govern themselves.
- **Direction disagreement.** The project goes somewhere a faction
  doesn't want to follow.
- **Stalled maintenance.** The original goes dormant; a fork picks up
  the work.
- **Corporate acquisition.** New owner, new priorities, community
  unease.

## Forks Worth Knowing

These are widely-documented, well-known cases. The point is the *lesson*
each teaches.

| Fork | Forked from | Driver | Lesson |
|---|---|---|---|
| **io.js** → merged back into **Node.js** | Node.js | Governance / release-pace dispute | A fork can be *leverage*: it forced the creation of the Node.js Foundation and open governance, then re-merged |
| **OpenTofu** | Terraform | License change (→ BSL) | Community + vendors fork fast when a permissive license goes source-available; foundation adoption follows |
| **Valkey** | Redis | License change (→ SSPL/source-available) | Major backers (and a foundation) can rally behind a fork almost overnight |
| **OpenSearch** | Elasticsearch | License change (→ SSPL) | A large cloud provider will fork to keep a permissive version it can offer |
| **MariaDB** | MySQL | Acquisition (Oracle buys Sun/MySQL) | Community hedges against corporate ownership by forking preemptively |
| **LibreOffice** | OpenOffice | Stewardship / acquisition concerns | A fork can become the *primary* project while the original fades |
| **Forgejo** | Gitea | Governance / commercialization dispute | Disagreement over a company taking control drives a community fork |
| **Jenkins** | Hudson | Trademark / control dispute (Oracle) | Trademark control is separate from code; the fork takes the community, the name stays behind |

(Years and exact details vary; treat these as the well-known shape, and
verify specifics if they matter to a decision.)

## The Patterns Across Them

### License changes trigger the fastest forks

The clearest modern pattern: a company relicenses a popular project from
permissive/OSI-approved to source-available (BSL, SSPL), and within days
a fork appears — often backed by other companies and a foundation. The
permissive license made the code *forkable*; the relicense made the
community *want* to. This is why
[open-core-dynamics.md](open-core-dynamics.md) matters before you depend
on a company-backed project.

### Governance disputes are about *power*, not code

io.js and Forgejo weren't about technical disagreement — they were about
*who decides*. When contributors feel locked out of governance, the fork
is their exit. The resolution (in io.js's case) was *better governance*,
which let the fork re-merge. Power-sharing prevents forks; concentration
invites them.

### Trademark ≠ code

A fork can take the code (the license allows it) but usually **not the
name** (trademark is separate — see [license-math.md](license-math.md)).
That's why forks get new names. It's also why the *original* name
sometimes withers while the renamed fork thrives — the community, not
the trademark, carries the value.

### Foundations as fork insurance

Many forks immediately seek a neutral foundation (LF, CNCF, ASF) home.
Foundation governance is precisely the protection against the
single-company control that caused the fork. A project already under a
foundation is far less fork-prone, because no one party *can*
unilaterally relicense or seize it.

## What Forks Teach You as a Contributor

- **Check the governance and license before investing.** A
  foundation-hosted, permissively-licensed project is fork-resistant and
  safe to build on. A single-company, CLA-assigned project carries
  relicense risk.
- **A fork can be the healthier project.** When evaluating where to
  contribute or what to depend on, the fork is sometimes where the
  active community and true-open license now live. Don't assume the
  original name is the right home.
- **The right to fork disciplines maintainers.** Knowing the community
  *can* leave keeps governance honest. It's a backstop, used rarely but
  decisively.
- **Forking is a last resort, not a tantrum.** Splitting a community is
  costly — duplicated effort, fragmented ecosystem, confused users.
  Healthy forks happen after good-faith attempts to resolve things
  inside the project fail. See [saying-no.md](saying-no.md) and
  [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md).

## When *You* Consider Forking

Rare, but it happens (a dead dependency you need, an unacceptable
relicense). Before you do:

- **Exhaust the alternatives.** Engage maintainers, propose, wait.
  Forking is expensive and divides effort.
- **Understand the maintenance burden.** You now own the whole thing —
  security, releases, the lot.
- **Respect the trademark.** New name, new branding.
- **Stay compatible if you can.** A drop-in fork is far more useful than
  a divergent one.
- **Consider a soft fork first.** Maintain a patch set on top of
  upstream rather than a hard split, if upstream is merely slow.

## Anti-Patterns

### Forking out of impatience

A maintainer hasn't merged your PR in a week, so you fork. That's not a
fork-worthy reason — it's a time-zone and patience issue (see
[time-zones.md](time-zones.md)).

### Depending on a single-company project without checking the license risk

Building critical infrastructure on a CLA-assigned, company-controlled
project, then being blindsided by a relicense. Know the model first.

### Assuming the original name is the canonical project

After a license-driven fork, the actively-developed, truly-open version
may be the renamed fork. Evaluate on governance and license, not on who
kept the name.

### Underestimating fork maintenance

A fork is a permanent obligation, not a one-time act. Most "I'll just
fork it" plans drown in the ongoing maintenance.

## See Also

- [open-core-dynamics.md](open-core-dynamics.md)
- [license-math.md](license-math.md)
- [governance.md](governance.md)
- [saying-no.md](saying-no.md)
- [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)
