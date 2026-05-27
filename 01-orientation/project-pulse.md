# Reading the Project Pulse

Before you invest hours in a repo, spend ten minutes reading its vital
signs. The same project can be a great place to contribute or a wasted
weekend depending on its current state.

## The Signals That Matter

### Commit frequency

```bash
git log --since="3 months ago" --oneline | wc -l
git log --since="3 months ago" --format='%cd' --date=short | sort | uniq -c
```

Interpretation:
- **Daily commits**: active, maybe chaotic.
- **Weekly commits**: healthy steady state for many projects.
- **Monthly commits**: maintenance mode.
- **Nothing in 6 months**: likely abandoned or "done."

"Abandoned" isn't automatically bad — small libraries can be feature-complete.
Match expectations to the project type.

### Issue and PR triage

GitHub examples (most can be done in the web UI too):

```bash
gh issue list --state open --limit 200 --json createdAt | jq '.[].createdAt' | sort | head
gh pr list --state open --limit 100 --json createdAt | jq '.[].createdAt'
```

What you're looking for:

- **Time-to-first-response** on issues — days vs months tells you everything.
- **Open issues with `stale` or `no-response` labels** — automated abandonment.
- **PRs sitting open for > 6 months** — slow merge cadence.
- **A growing backlog with no triage** — maintainer overload.

### Bus factor

```bash
git shortlog -sn --since="1 year ago" | head -20
```

Read this carefully:

- **One name with 95%** = critical bus factor. If that person stops, the project stops.
- **Top 3 covering 80%** = small team, fragile.
- **A long tail of contributors** = healthy community, distributed knowledge.

For a project you're considering depending on, bus factor is a real risk.

### Maintainer responsiveness

Look at the last 10 closed PRs:

- How long from open to first review?
- How many round-trips before merge?
- Does the maintainer engage in technical discussion, or just merge/close?
- Is the tone friendly, neutral, or terse?

This is the experience *you* will have. Calibrate accordingly.

### Releases and versioning

- **Frequent releases** (weekly/biweekly): the project ships. Easy to consume.
- **Rare releases** (yearly+): you may need to depend on `main` or fork.
- **Pre-1.0 forever**: signals either "we're cautious" or "we never finished."
- **Major versions every 6 months**: signals breaking-change culture; plan migrations.

### Test discipline

In the most recent PRs:

- Are tests added with changes?
- Does CI run on PRs? Does it block merge on failure?
- Is the test suite real (lots of assertions) or theater (mock-heavy)?

A project that doesn't enforce tests will accumulate regressions, and
your bugfix may be against an already-broken code path.

### Documentation freshness

- Is the README's quick-start still accurate?
- Are there docs from 5 versions ago that contradict current behavior?
- Are there generated API docs and are they current?

Stale docs aren't a deal-breaker, but they tell you "we don't have
bandwidth for docs," which often correlates with other gaps.

## Composite Signals

### Healthy active project

- Daily-to-weekly commits.
- Issues triaged within a week.
- PRs reviewed within 1–2 weeks.
- Many contributors over the last year.
- Recent releases, with notes.

This is the easy case. Standard contribution workflows work.

### Healthy maintenance mode

- Sparse but regular commits.
- Long response times but consistent.
- Few open issues — triage is happening, just slowly.
- Releases when needed.

Smaller libraries often live here. Expect slower interactions and adjust
your pings accordingly.

### Stressed / understaffed

- Many open issues, growing.
- Long unaddressed PRs.
- Stale-bot active.
- One or two maintainers visible.

Your PR will need to be excellent and small to get attention. Avoid
opening unsolicited large changes.

### Abandoned

- No commits in many months.
- No issue triage.
- Owner unresponsive in any channel.

Two paths:
- **Use the project as-is**, accepting no future fixes.
- **Find or create a fork** that's maintained.

Don't open PRs against abandoned repos and expect engagement. If you need
the fix, fork.

### "Vendored" / Single-vendor open source

- One company's name everywhere.
- Roadmap aligned with their commercial product.
- PRs from outside contributors merged inconsistently.
- License recently changed (BSL, SSPL).

Not bad — just real. Outside contributions are guest contributions; treat
the project's direction as not yours to set. See
[../13-hidden-knowledge/open-core-dynamics.md](../13-hidden-knowledge/open-core-dynamics.md).

## Side Channels

The repo isn't the whole project. Check:

- **Discord / Slack / Matrix** — community pulse, maintainer presence.
- **Mailing list** — for older or more formal projects (Postgres, kernel).
- **Conference talks** — recent talks signal direction.
- **Blog / newsletter** — often where maintainers explain "why."
- **Community calls** — monthly/quarterly, sometimes with minutes posted.

A repo that looks dead might have an active Discord. A repo that looks
active might have all its real decisions happening in private Slack.

## Practical Decision Tree

You found a project. Should you contribute?

1. **Is it active enough that PRs land?** Check last 10 merged PRs' age.
2. **Is the issue I care about already known?** Search closed first.
3. **Is the maintainer responsive to outside contributions?** Sample recent
   conversations.
4. **Is the project's direction compatible with my change?** Read CHANGELOG,
   recent talks.
5. **Am I willing to wait their cycle time?** Plan for 4–8 weeks if median.

If yes to all: open with confidence. If some no's: open a discussion or
issue first to gauge interest before sinking time.

## Anti-Patterns

- **Reading only commit counts.** A project with one huge commit a month
  might be doing more work than one with daily noise.
- **Judging by stars.** Stars correlate with hype, not health.
- **Ignoring side channels.** Some projects live in Discord, with the
  repo as artifact.
- **Assuming silence = rejection.** Often it's bandwidth, not opinion.

## See Also

- [../08-maintainers/](../08-maintainers/) — adapting your interaction style to maintainer state
- [../13-hidden-knowledge/governance.md](../13-hidden-knowledge/governance.md) — formal decision-making
- [../13-hidden-knowledge/burnout.md](../13-hidden-knowledge/burnout.md) — recognizing it in others
