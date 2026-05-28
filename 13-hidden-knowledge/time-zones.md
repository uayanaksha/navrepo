# Time Zones

Open source is global and asynchronous. A maintainer's silence usually
means they're asleep, not ignoring you. Calibrating your expectations to
the project's geography keeps you patient and keeps you from
self-sabotaging with impatient pings.

## The Async Reality

The contributor you're waiting on may be:

- Twelve time zones away (your morning is their midnight).
- Doing this as a volunteer, in evenings and weekends only.
- On a national holiday you don't share.
- Heads-down on deep work, checking notifications once a day.

A reply that takes three days isn't rudeness or rejection. It's the
normal cadence of unpaid, distributed, asynchronous collaboration. The
maintainer who answers in an hour is the exception, not the baseline.

## Estimating a Project's Rhythm

Before you wonder why no one's replied, gauge the project's actual
cadence:

| Signal | How to read it |
|---|---|
| Commit timestamps | When (and how often) do maintainers actually work? |
| Maintainer location | Profile/blog often says; sets the working hours |
| Time-to-first-response on recent issues | The realistic baseline |
| Release frequency | Active weekly vs. quarterly bursts |
| Number of active maintainers | One person = slow; a team = faster |

```bash
# When do commits actually land? (hour-of-day histogram)
git log --pretty='%ad' --date=format:'%H' | sort | uniq -c

# Recent activity cadence
git log --since='30 days ago' --pretty='%an %ad' --date=short
```

If the last three issues took a week for a first reply, yours will too.
That's your calibration, not a slight.

## Working Across Zones

When the round-trip is 24+ hours, each exchange is expensive. Make each
one count:

- **Front-load context.** A complete first message (repro, environment,
  what you tried) avoids a clarifying round-trip that costs another day.
  See [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md).
- **Ask all your questions at once.** Don't dribble them out one per
  day; batch them.
- **Propose, don't just ask.** "I think the fix is X; does that match
  your intent?" lets them answer yes/no in one cycle instead of opening
  a discussion.
- **State your assumptions and proceed.** "Assuming you want backwards
  compat, I'll do Y unless you say otherwise" keeps work moving during
  the gap.

The goal: minimize the number of slow round-trips to resolution.

## Ping Etiquette, Time-Zone Edition

The full etiquette is in
[../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md);
the time-zone-specific part:

- **Wait at least a full working week** before a polite follow-up on an
  unblocked review. The maintainer may not have hit their "your
  timezone's" working hours yet, several times over.
- **One ping, then patience.** A daily "any update?" across time zones
  is just noise arriving while they sleep.
- **Don't read silence as "no."** Silence across zones is almost always
  "not yet," not "rejected."

## Synchronous Windows

Occasionally you need real-time (a hard debugging session, a design
discussion). Then:

- **Find the overlap.** A world-clock tool reveals the few hours you
  share. Propose specific UTC times ("14:00 UTC — does that work for
  you?"), not "your afternoon."
- **Respect that the overlap may be tiny or zero.** If you're 12 hours
  apart, someone's taking an inconvenient call. Offer, don't demand.
- **Default back to async.** Most things don't need sync at all.
  Reserve real-time for what genuinely can't be done in writing.

## Always Use UTC (or Be Explicit)

"Let's talk tomorrow afternoon" is meaningless across zones. Eliminate
ambiguity:

- State times in **UTC**, or with an explicit zone *and* offset.
- Better: send a time-zone-aware link/tool that renders in each
  person's local time.
- In writing, prefer **absolute dates** ("by 2026-06-03") over relative
  ones ("by end of week") — "this week" differs by where you are.

## Cultural Calendar Awareness

Responsiveness drops around holidays you may not observe — and they vary
by country. A project may go quiet for reasons that have nothing to do
with you:

- Year-end / new-year periods (varies by culture and calendar).
- National holidays specific to the maintainer's country.
- Local vacation seasons (e.g., much of Europe slows in mid-summer).

If a project goes unusually quiet, a holiday somewhere is a likelier
explanation than a problem with your contribution.

## Anti-Patterns

### Reading silence as rejection

The most common misread. Across time zones, no reply almost always
means "not yet," not "no." Wait before concluding anything.

### Impatient pinging

"Any update?" every day lands in their inbox at 3 a.m. and signals
impatience without speeding anything up. One polite follow-up after a
week.

### Dribbling questions

One question per round-trip turns a 1-day resolution into a 5-day one
when each cycle costs 24 hours. Batch everything into one message.

### Vague, zone-relative times

"Tomorrow afternoon" means five different things to five people. Use
UTC and absolute dates.

## See Also

- [../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md)
- [../08-maintainers/postures.md](../08-maintainers/postures.md)
- [../08-maintainers/channels.md](../08-maintainers/channels.md)
- [burnout.md](burnout.md)
