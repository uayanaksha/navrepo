# Conference Talks

Conference talks are where maintainers say out loud what they'd never
write in an issue: where the project is *really* headed, what they
regret, what they're about to rewrite, and the philosophy behind their
decisions. Watching the recent ones is the cheapest way to understand a
project's mind.

## Why Talks Reveal So Much

A talk is a different medium than a repo, and it loosens different
information:

- **It's forward-looking.** Issues track what's filed; talks pitch
  what's *intended*. Maintainers use talks to telegraph direction.
- **It's candid.** "Here's what we got wrong and what we're changing"
  shows up in talks far more than in docs.
- **It's reasoned.** A talk explains the *why* behind decisions you only
  see the *what* of in the code.
- **It's curated by the maintainers themselves.** They chose what to say
  — so it's a direct signal of what they think matters.

This is a primary source for the hidden roadmap (see
[hidden-roadmaps.md](hidden-roadmaps.md)).

## What to Mine From a Talk

When you watch a recent talk by a project's maintainer, listen for:

| Signal | What it tells you |
|---|---|
| "We're working on / planning X" | The actual near-term roadmap |
| "We're rewriting / replacing Y" | Don't build on Y; it's going away |
| "We made a mistake with Z" | A known pain point; possibly a welcome fix area |
| "The philosophy is…" | The taste your contribution must fit |
| "We will never do W" | A non-goal — don't propose it |
| What they're *excited* about | Where energy and review attention go |
| What they apologize for | Where help may be genuinely welcome |

## Watch Before You Propose Anything Big

The expensive mistake (again): building a major feature, then learning
from a six-month-old talk that the whole subsystem is being replaced, or
that your idea is an explicit non-goal. Ten minutes of a recent talk can
save weeks.

Before a significant proposal:

1. **Find recent talks** by core maintainers (the last year or two).
2. **Watch at 1.5–2x**, listening for the signals above.
3. **Check whether your idea aligns** with the stated direction and
   philosophy.
4. **Reference what you learned** when you propose: "I saw in your
   recent talk that you're moving toward X — this fits by…" This signals
   you've done the homework and instantly raises your credibility with
   maintainers.

## Where to Find Them

Without inventing specific URLs, the reliable places:

- **Major language/ecosystem conferences** post recordings (often on
  the conference's video channel) — most ecosystems have flagship
  events.
- **The project's own site / blog / wiki** often links "talks" or
  "media."
- **Search** the project name plus "talk," "keynote," or the maintainer's
  name plus the year.
- **Podcasts and recorded streams** — many maintainers appear on
  developer podcasts, which are even more candid than stage talks.
- **Release/announcement streams** for big projects.

## Beyond the Roadmap: Talks as Learning

Talks aren't only for reconnaissance. A good talk by an expert is a
high-density way to learn a technology or a problem space — often better
than docs because it conveys the *mental model* and the *why*, not just
the API. This connects to the compounding-reading habit (see
[compounding-reading.md](compounding-reading.md)) and to learning
unfamiliar tech (see
[../09-unknown-tech/learning-paths.md](../09-unknown-tech/learning-paths.md)).

When learning a new project or domain, "watch the canonical talk by its
creator" belongs near the top of your learning path.

## A Caution on Staleness

Talks are snapshots in time. A three-year-old talk may describe a
direction that's since changed. Weight by recency:

- **Recent talk (< ~1 year):** likely current direction.
- **Older talk:** good for philosophy and history; verify the specifics
  against current code and recent changelogs (see
  [../02-navigation/git-archaeology.md](../02-navigation/git-archaeology.md)).

Cross-check what a talk claims against what the project has *actually
shipped* since.

## Anti-Patterns

### Proposing without checking talks

Building something the maintainers announced they're killing — knowable
from a recent talk you didn't watch. Watch first.

### Trusting an old talk as current

Acting on a years-old roadmap pitch. Directions change; verify against
recent shipping activity.

### Ignoring stated philosophy

A talk lays out the project's values; proposing something that violates
them gets declined. Fit your contribution to the philosophy you heard.

### Treating talks as the *only* source

Talks complement, not replace, the changelog, issues, and code. Triangulate.

## See Also

- [hidden-roadmaps.md](hidden-roadmaps.md)
- [compounding-reading.md](compounding-reading.md)
- [../09-unknown-tech/learning-paths.md](../09-unknown-tech/learning-paths.md)
- [../10-features-refactors/proposals-and-rfcs.md](../10-features-refactors/proposals-and-rfcs.md)
