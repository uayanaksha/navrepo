# Ping Etiquette

How and when to follow up. Get this wrong and you become "that
contributor." Get it right and maintainers remember you fondly.

## The Time Schedule

| Time since last activity | Action |
|---|---|
| < 1 week | Wait. Don't ping. |
| 1–2 weeks | Optional gentle ping if blocking |
| 2–4 weeks | First ping, polite |
| 4–6 weeks | Second ping, with options |
| 6–8 weeks | Offer to close |
| 8+ weeks | Close gracefully, leave door open |

This is approximate. Match the project's pace — some projects always
respond within days; others within months.

## What a Good First Ping Looks Like

After ~2 weeks:

> "Friendly ping — happy to make any changes here, or close if it's
> not a fit for the project. No rush; just wanted to surface it in
> case it got buried."

Notice:
- "Friendly" — sets tone.
- Acknowledges they might have reasons.
- Offers to close — gives them an easy out.
- "No rush" — relieves pressure.

## The Second Ping

After ~6 weeks:

> "I'll close this if I don't hear back by <date>, no hard feelings.
> Easy to reopen if it becomes useful."

Now you're escalating to "I respect your time; I also respect my own."

## The Close

After ~8 weeks:

> "Closing for now — no concerns if it makes sense to revisit later.
> Thanks for considering."

Don't editorialize. Don't blame. A clean close keeps the door open
for next time.

## What Not to Ping About

### Don't ping for "just to check"

If there's no new information:

❌ "Any update?"
❌ "Hi, checking in!"
❌ "Bump."

These add noise without value. The maintainer has the PR; they know
it exists.

### Don't ping at the project level

❌ "@every-maintainer can someone look at this?"
❌ Posting your link in unrelated issues.
❌ Filing a new issue asking why your PR isn't reviewed.

Stay in the PR.

### Don't ping multiple maintainers

@-mentioning two or three maintainers in your ping:

❌ "@alice @bob @charlie any thoughts?"

Pick one (usually the one who reviewed last, or your CODEOWNER), or
post without specific @-mentions.

### Don't ping outside business hours / timezones

If you know the maintainer's timezone, respect it. Sending at 3am
their time signals desperation.

## Pings That Add Value

### "I have new info"

> "Quick update — found this also happens with empty input. Pushed a
> fix for that case."

A push + summary is more valuable than a plain ping.

### "I'll be unavailable"

> "Heads up — I'm out next week. Will pick up any feedback when I
> return."

Sets expectations; not a ping per se, but communication.

### "Stuck on a specific question"

> "I think this is ready, but I'm uncertain about [specific thing].
> Would you prefer approach A or B?"

A specific question is easier to answer than "any feedback?"

### Linking related context

> "FYI — this PR addresses what was discussed in #1234 (closed). New
> approach reflects that thread."

Helpful pointers, not just bumps.

## Channel for Pings

### PR comments

Default. Most projects expect pings in the PR.

### Issue comments

For issues, ping in the issue.

### Public chat (Discord, Slack)

For projects with active community channels, a polite mention there
can work:

> "Hi! Anyone available to look at PR #1234 when convenient? Tagging
> in case it got buried."

Use sparingly. Don't make this your primary channel.

### DMs

Avoid for substantive content. Use only for:
- Coordinating timing ("can you review this on Wednesday?").
- Security issues per `SECURITY.md`.
- Personal context ("hey, your turn went badly — recover well").

### Email

Most projects don't use email. Some older ones (mailing-list-based)
do. Match what the project uses.

## When Maintainer Pings You

Sometimes the maintainer follows up on you:

> "Hi — any progress on this? Happy to help if stuck."

Respond promptly. Even "Tied up this week; will pick up by Friday"
is fine.

If you've abandoned the work, say so:

> "Stalled out on this; lost time to fix it. Feel free to close or
> assign to someone else."

Honest abandonment is better than ghosting.

## Pings for Different Channels

### GitHub: keep it in-thread

Reply to the existing PR / issue. Don't open a new issue to ping.

### Mailing list: respect threading

Reply to the message. Don't start a new thread.

### Discord / Slack: don't @ everyone

`@here` and `@channel` are for emergencies. A "Hi all, anyone
available for review?" message that doesn't ping everyone is fine.

## When Multiple Pings Have Been Ignored

If you've pinged twice and gotten no response, the message is clear:
they're not engaging.

Don't take a third ping as the answer. Close politely:

> "Closing — totally understand bandwidth. If this becomes relevant,
> happy to reopen."

You've done your part. Moving on isn't failure.

## A Polite Ping Cheat Sheet

```
"Friendly ping —"
"Quick check-in —"
"Wanted to surface this in case it got lost —"
"No rush, just flagging —"
```

```
"Happy to make any changes."
"Happy to close if it's not a fit."
"Happy to come back to this later."
```

```
"Thanks for your time."
"Appreciate your work on the project."
"Will plan to close by <date>."
```

Mix and match. Adapt to tone.

## Edge Cases

### Critical bug fix

For a security or production-critical fix:

> "Pinging — this fixes [specific incident]. If anyone's available
> for fast-track review I'd appreciate it. If not, here's my fork
> with the patch applied in the meantime."

Provide the workaround so the urgency doesn't pressure them.

### Time-sensitive (release blocker)

> "Wanted to flag — this is on the path of v2.0 release if I
> understand the milestones right. Should it be re-prioritized or
> moved to v2.1?"

Frame it as helping their planning, not demanding action.

### Multiple maintainers, you don't know who

A neutral ping:

> "Friendly ping — if any maintainer has bandwidth, would appreciate
> a look. Otherwise happy to close."

No specific @ mention. Whoever's around can grab it.

## See Also

- [postures.md](postures.md) — the why behind the etiquette
- [../07-pull-requests/long-running-prs.md](../07-pull-requests/long-running-prs.md)
- [burnout-awareness.md](burnout-awareness.md) — why patience matters
