# Compounding Reading

The developers who seem to "just know things" usually have a quiet habit
in common: they read widely and continuously, and it compounds. Small,
regular inputs — changelogs, postmortems, a new codebase now and then —
add up to a deep, transferable model of how software works.

## The Compounding Effect

Knowledge in software compounds like interest:

- Each codebase you read makes the *next* one faster to understand (you
  recognize the patterns).
- Each postmortem you read makes you spot the next failure mode earlier.
- Each changelog you follow keeps you current as the ground shifts.

A little every week beats a cram session, because the returns accrue on
top of each other. The gap between someone who reads regularly and
someone who doesn't widens every year.

## High-Value Reading Habits

### Read changelogs and release notes

When you update a dependency, *read what changed*. It's a tiny habit
with outsized return:

- You learn the library's evolving capabilities and idioms.
- You catch deprecations and breaking changes *before* they bite.
- You absorb how a well-run project communicates change.

```bash
# Don't just bump the version — read what you're pulling in
# (changelog, release notes, or the git log between versions)
git log v1.4.0..v1.5.0 --oneline      # in a vendored/checked-out dep
```

Following a few key dependencies' release notes keeps you ahead of the
churn instead of behind it.

### Read postmortems

Public incident postmortems are some of the best engineering writing
available — concrete failures, root causes, and lessons, written by
teams operating at scale. Reading them:

- Teaches failure modes you can then *avoid* (cheaper than learning them
  in production).
- Builds intuition for distributed-systems and operational risk (see
  [../14-advanced/working-in-distributed-systems.md](../14-advanced/working-in-distributed-systems.md),
  [../14-advanced/incident-response.md](../14-advanced/incident-response.md)).
- Shows what *blameless* analysis looks like.

Many companies publish them; collections of notable postmortems are
easy to find. A postmortem a week is a cheap, high-density education in
how systems really break.

### Read one new codebase a month

Deliberately read a project you admire or depend on, the way you'd study
a craftsman's work (see
[../12-mindset/reading-vs-writing.md](../12-mindset/reading-vs-writing.md)).
You're not fixing anything — you're learning:

- How they structure things.
- How they test.
- How they handle the hard parts (concurrency, error handling, APIs).

Each one adds patterns to your repertoire that transfer everywhere. Use
the techniques in [../03-reading-code/](../03-reading-code/) to do it
efficiently.

### Follow the conversation

- **RFCs / design docs** of projects you use — watch decisions get made
  in real time.
- **Maintainer blogs and talks** — direction and philosophy (see
  [conference-talks.md](conference-talks.md)).
- **A few high-signal newsletters or aggregators** in your ecosystem —
  curation saves you the firehose.
- **Source of your dependencies** — when a library surprises you, read
  *why* in its code rather than guessing.

## Make It Sustainable

The habit only compounds if it survives. Keep it light:

- **Timebox it.** Twenty minutes a few times a week beats an unsustainable
  daily hour you'll abandon.
- **Keep a reading queue**, but don't let it become a guilt list. Drop
  what's no longer interesting.
- **Take notes you'll actually revisit** — a short "what I learned" beats
  highlights you never reopen (see
  [../03-reading-code/note-taking.md](../03-reading-code/note-taking.md)).
- **Read with a question in mind** when you can — directed reading sticks
  better than aimless scrolling.

## Curation Over Consumption

The goal isn't to read *everything* — that way lies burnout and FOMO.
It's to read *the right things consistently*:

- Depth over breadth: one postmortem understood beats ten skimmed.
- Primary sources over hot takes: the actual changelog, the actual code,
  the actual design doc.
- Signal over recency: a foundational paper or a classic postmortem
  outvalues today's noise.

## The Long Game

This is the input side of the multi-year arc in
[../14-advanced/building-reputation.md](../14-advanced/building-reputation.md).
The engineer with a decade of compounding reading has a model of the
field that can't be crammed — it was *accreted*. Start the habit now and
let time do the work; the cost is small and the return is enormous and
permanent.

## Anti-Patterns

### Bumping dependencies without reading changelogs

Blind version bumps mean breaking changes and deprecations ambush you in
production. Read what you're pulling in.

### Doom-reading the firehose

Trying to keep up with *everything* leads to burnout and shallow
skimming. Curate ruthlessly; go deep on less.

### Hoarding without absorbing

A 500-item read-later list you never open isn't reading. Read fewer
things, actually.

### Only reading your own stack

Never reading outside your language/domain leaves you provincial.
Occasionally read something foreign — it transfers more than you'd
expect.

## See Also

- [conference-talks.md](conference-talks.md)
- [../12-mindset/reading-vs-writing.md](../12-mindset/reading-vs-writing.md)
- [../03-reading-code/note-taking.md](../03-reading-code/note-taking.md)
- [../14-advanced/reading-academic-papers.md](../14-advanced/reading-academic-papers.md)
- [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)
