# Activity vs Progress

Looking busy and being useful are different things. Open source and
engineering both reward *progress* — the problem actually moved — but
both are full of incentives to optimize for *activity*, the visible
appearance of work. Knowing the difference keeps you honest.

## The Distinction

| Activity | Progress |
|---|---|
| Commenting on many issues | Resolving one |
| Many small commits | One coherent, correct change |
| Being in every thread | Moving the thread that's stuck |
| Quick reactions | Considered contributions |
| Reopening debates | Closing decisions |
| "Working on it" updates | The thing being done |

Activity is *legible* — it shows up in notification feeds, contribution
graphs, and standup updates. Progress is often *quiet* — a hard problem
understood, a design clarified, a gnarly bug actually fixed.

## Why Activity Is Seductive

- **It's visible.** A comment gets a reaction; a week of silent reading
  doesn't.
- **It feels productive.** You did *things* today. The fact that none of
  them moved the real problem is easy to ignore.
- **It's rewarded by metrics.** Contribution graphs, comment counts, PR
  counts — all measure activity, none measure progress.
- **It's lower-risk.** A reaction can't be wrong. Solving the hard
  problem can.

The result: a strong pull toward busy work that *feels* like
contribution but moves nothing.

## Deep Work Is Often Invisible

The most valuable work frequently produces no visible activity for a
while:

- Reading the codebase deeply before a big change (days of "nothing").
- Understanding a subtle bug before the one-line fix.
- Thinking through a design before writing the RFC.
- Reproducing a flaky failure reliably.

These quiet stretches are not idleness — they're the expensive part.
The visible artifact (the small PR, the clear RFC) is the cheap tip of
a large iceberg. Judge yourself and others by the iceberg, not the tip.

## Don't Comment for Visibility

A specific trap in open source and large teams: commenting to be *seen*
rather than to *help*.

- "+1" / "any update?" / "I also want this" on an issue: adds noise,
  notifies everyone subscribed, moves nothing. (Use the reaction button
  for "+1".)
- Restating what someone already said, to be in the thread.
- Jumping into a design discussion you haven't read fully, to have a
  presence.

Before commenting, ask: **does this move the conversation forward, or
just add my name to it?** If it's the latter, stay quiet. Silence is
fine. A thread with ten "+1"s and no new information is worse than one
with none.

When you *do* have something — a reproduction, a concrete proposal, a
relevant constraint others missed — say it. That's progress, and it's
welcome.

## Measuring Yourself Honestly

At the end of a week, the activity question is "what did I do?" The
progress question is **"what is now true that wasn't true before?"**

- "I commented on twelve issues" → activity.
- "The deadlock in the scheduler is fixed and can't recur" → progress.
- "I read the auth module and now understand the session model" →
  progress (even with zero commits).

The second framing is uncomfortable because some busy weeks produce
little progress. That discomfort is the signal working correctly.

## For Maintainers and Reviewers

The same lens applies to how you evaluate others:

- A contributor with one careful, complete PR is worth more than one
  with twenty noisy ones.
- A quiet contributor who shows up when it counts is an asset, not a
  slacker.
- Don't reward comment-volume or PR-count; reward problems solved.

Contribution graphs are a famously bad proxy for value. Plenty of
essential work — review, mentoring, design, triage — barely registers on
them.

## Sustainable Pace

Optimizing for progress over activity is also kinder to you. You don't
have to be *visibly* working at all times. You can have quiet days of
reading and thinking without guilt, because you're measuring the right
thing. Performative busyness is exhausting and, ultimately, hollow.

## Anti-Patterns

### Notification-driven work

Letting the loudest thread set your priorities means you optimize for
responsiveness, not impact. The important work is often in the quiet
corner with no notifications.

### Contribution-graph farming

Commits made to keep a streak green are activity theater. Nobody
serious is impressed, and it trains bad habits (tiny meaningless
commits, work split to look like more).

### The standup performance

Narrating activity ("I looked into X, then Y, then Z") to sound busy,
when the honest update is "still stuck on the hard part." The honest
update is more useful to everyone.

### Comment-for-presence

Adding to threads to be seen. It's noise, it notifies people, and it
dilutes the signal. Contribute substance or stay quiet.

## See Also

- [reading-vs-writing.md](reading-vs-writing.md)
- [no-drive-bys.md](no-drive-bys.md)
- [../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md)
- [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)
