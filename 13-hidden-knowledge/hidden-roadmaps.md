# Hidden Roadmaps

The roadmap that governs what gets merged is rarely the `ROADMAP.md` in
the repo. The real one lives in maintainers' heads, private chats, and
conference talks. Find it before you build something big — or watch your
PR get declined for "not fitting our plans."

## Why the Public Roadmap Is Incomplete

The documented roadmap (if one exists) is a lagging, sanitized snapshot:

- It's updated rarely; real plans move faster.
- It omits the controversial or unannounced.
- It can't capture the *taste* and *direction* that actually drive
  decisions.
- Maintainers think years ahead in conversations they never write down.

So the issue tracker shows you what's been *filed*, not what the
maintainers *intend*. Those are different, and the gap is where good PRs
go to die.

## Where the Real Roadmap Lives

| Source | What it reveals |
|---|---|
| Recent **conference talks** | Direction, planned rewrites, philosophy (see [conference-talks.md](conference-talks.md)) |
| Maintainer **blog posts** | Reasoning behind decisions; what they're excited about |
| **Discord / Slack / Zulip / IRC** | Day-to-day planning, "we're thinking about X" |
| **Pinned issues / discussions** | Curated "here's where we're headed" |
| **Milestones / project boards** | What's slated for the next release |
| Mailing lists / **RFC repos** | Formal future proposals under debate |
| Maintainers' **social media** | Offhand "we should really do Y" |
| **Closed** PRs/issues with "not now" | What they've explicitly deferred |

The roadmap is reconstructed from these, not read from one file.

## Check Before You Propose

The expensive mistake: building a large feature, opening the PR, and
hearing "thanks, but we're actually rewriting this whole subsystem next
quarter" or "this is intentionally out of scope." All that work, wasted,
because the plan was knowable and you didn't look.

Before any significant proposal:

1. **Search closed issues/PRs** for your idea. It may have been
   proposed and declined, with reasoning. (See
   [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md).)
2. **Watch the most recent talk** by a maintainer. Ten minutes can
   reveal the whole direction.
3. **Read the last few release notes / changelogs.** The trajectory is
   visible in what they've been shipping.
4. **Skim the chat / discussions** for your topic.
5. **Ask, in an issue, before building:** "I'm thinking of doing X —
   does this fit your direction?" One question saves weeks.

## Reading Direction from Signals

Even without explicit statements, direction leaks:

- **What gets merged** reveals what's welcome. A pattern of merged
  performance PRs and declined feature PRs tells you the maintainers
  prioritize performance and resist scope growth.
- **What gets declined** reveals boundaries. Read the rejections.
- **What maintainers personally work on** is where the energy and
  interest are.
- **Deprecations and removals** reveal what they're moving *away* from
  — don't build on top of it.

## The "Intentional Non-Goal"

Many projects have explicit *non-goals* — things they will never do, on
purpose. A minimalist library won't add your batteries-included
feature; a security-focused tool won't add the convenient-but-risky
option. These are often documented in a README "Philosophy" or
"Non-Goals" section, or stated repeatedly in closed issues.

Proposing a non-goal is the fastest rejection there is. Find the
non-goals first. "Won't fix — out of scope" is usually a non-goal
collision (see [issue-closure-reasons.md](issue-closure-reasons.md)).

## Timing Your Contribution

The roadmap also tells you *when*:

- If they're mid-rewrite of a subsystem, don't send PRs against the old
  one — they'll be thrown away.
- If a major release is imminent, breaking changes might be welcome
  *now* and impossible later.
- If they just shipped a big release, they may be in a stabilization
  phase that wants fixes, not features.

Aligning with the project's current phase dramatically raises your odds.

## Anti-Patterns

### Building big, then asking

Sinking weeks into a feature before confirming it fits. Ask first; it's
one comment and it saves the weeks.

### Trusting `ROADMAP.md` as current

A stale roadmap file misleads more than no file. Corroborate with recent
talks, changelogs, and chat.

### Proposing a known non-goal

Pitching the thing the project has explicitly and repeatedly said it
won't do. Read the closed issues and the philosophy section first.

### Ignoring what they actually ship

The merged-PR history is the most honest roadmap there is. If they
never merge features like yours, that's your answer.

## See Also

- [conference-talks.md](conference-talks.md)
- [maintainer-calculus.md](maintainer-calculus.md)
- [issue-closure-reasons.md](issue-closure-reasons.md)
- [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)
- [../10-features-refactors/proposals-and-rfcs.md](../10-features-refactors/proposals-and-rfcs.md)
