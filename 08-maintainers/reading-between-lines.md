# Reading Between the Lines

Maintainers often can't say what they really mean. Learning to read
the subtext makes you a better collaborator.

## Why They Don't Say It Plainly

- **Bandwidth**: writing nuance takes time.
- **Diplomacy**: they don't want to seem dismissive.
- **Hope**: maybe with rework, this could be useful.
- **Liability**: a flat "no" is harder than a soft delay.
- **Culture**: some norms favor indirectness.

The result: you have to parse what they're saying *and* what they mean.

## Common Subtexts

### "I'm not blocking — I just haven't read this yet."

Signs:
- No response for weeks.
- Eventually a "thanks, I'll take a look" with no follow-through.

What they mean: this isn't a priority but I can't say so.

Your read: it's de facto blocked. After a few more weeks, close
gracefully.

### "I don't want this feature but I don't want to argue."

Signs:
- Silence after initial polite responses.
- "Interesting!" with no further engagement.
- Vague concerns that don't lead anywhere.

What they mean: I'm hoping you give up.

Your read: probably not going to merge. Verify with a direct question
("Should I close this? Or is there a path forward?"). Accept the
answer.

### "The timing is bad."

Signs:
- "We're in the middle of [other refactor], can you wait?"
- "This depends on #N which is in flight."
- "Let's revisit after the v2 release."

What they mean: come back later. Or never.

Your read: ask for a target date. If they give one, mark your
calendar. If they're vague, the PR may not survive.

### "I don't agree but I'll merge anyway."

Signs:
- Slightly terse approval.
- "OK, merging — let's see how it goes."
- No follow-up engagement.

What they mean: they think you might be wrong but are letting you try.

Your read: this isn't full endorsement. Watch for follow-up issues
suggesting they were right.

### "This conflicts with work I haven't pushed yet."

Signs:
- "I have something similar in flight."
- "Let me check with my own branch."
- Long silence followed by an unrelated commit that contradicts your
  PR.

What they mean: they had something they didn't share publicly.

Your read: ask "do you have something in flight here? Happy to align
or wait."

### "I'm overwhelmed."

Signs:
- Many PRs sitting.
- Issues unanswered.
- Increasingly short replies.
- Stale-bot active.

What they mean: bandwidth crisis.

Your read: don't pile on. Offer help if you can ("happy to triage
some issues if useful"). Or wait.

### "This isn't ready yet."

Signs:
- Many small comments without saying "approve."
- "Could you also..." chains that don't end.
- "Have you thought about..."

What they mean: not yet, but no specific blocker.

Your read: continue iterating. Or ask: "What would 'ready' look like?
Want to make sure I'm aiming at the right target."

### "Your understanding might be wrong."

Signs:
- Question that seems weirdly basic ("are you sure this happens?").
- Asking you to provide a reproducer when the bug is well-described.
- Linking to docs you'd think they'd know about.

What they mean: they suspect user error or misunderstanding.

Your read: re-verify. Provide a reproducer. Be open to being wrong.

### "I appreciate this but I can't do it for you."

Signs:
- "Welcome to push a PR!"
- "We'd accept this if someone did the work."

What they mean: it's on you to drive.

Your read: do the work, or accept the gap exists.

## Tone Indicators

| Phrase | Often means |
|---|---|
| "Interesting." | I'm not engaging with this. |
| "Could you maybe..." | Please change this. |
| "I see what you mean..." | But I disagree. |
| "We've discussed this before." | Don't bring it up again. |
| "Out of scope for this PR." | Don't argue; split. |
| "Worth thinking about." | Won't happen. |
| "Maybe we can revisit later." | Probably never. |
| "Thanks for the contribution!" (and nothing else) | Polite close incoming. |
| "Have you considered X?" | I think X is better. |
| "I'd be cautious about..." | Don't do this. |

These aren't universal. Different maintainers use different idioms.
Calibrate to your specific maintainer over time.

## Reading the Cadence

The *pacing* of responses tells you a lot:

- **Same-day replies that gradually slow**: engagement is fading.
- **Bursty engagement (lots, then nothing)**: bandwidth-driven.
- **Consistent slow**: it's their normal pace. Don't read into it.

A maintainer who suddenly stops replying to your PR (but replies to
others) is likely sending a signal.

## Reading the Sample Size

One terse reply might be a bad day. Five terse replies are a pattern.

When evaluating subtext, look at *what's normal* for this maintainer:

- Read their other PR reviews.
- See how they engage with other contributors.
- If they're always terse, your terse reply isn't a signal.

## When in Doubt, Ask Directly

When subtext is genuinely confusing:

> "Want to make sure I'm reading you right — should I keep iterating,
> or is this not a direction you want? Either is fine; just want to
> spend my time well."

This invites them to say no clearly. Most will, when given the easy
out.

## Indirect Yes vs Indirect No

Indirect yeses sound like:
- "This could be useful."
- "Open to seeing what this looks like."
- "Try it; we'll see."

Indirect noes sound like:
- "Let me think about it."
- "Interesting approach."
- "We've discussed this before."

When you're not sure: assume indirect noes are noes. Better to be
pleasantly surprised than to invest in something that won't merge.

## Cultural Variation

Some cultures default to indirectness; some to directness. Norms:

- **East Asian, British, Indian (often)**: more indirect; refusal
  softened.
- **Dutch, German, Israeli (often)**: more direct; "no" is "no."
- **American (often)**: middle, with cultural-business politeness.

These are generalizations and individuals vary. Calibrate to the
person, not the stereotype.

## Becoming Easier to Read

You can help your maintainer by being easy to read:

- When you mean no, say so.
- When you're done, say so.
- When you're stuck, say so.
- When you're frustrated, *don't* say so — but step away.

Direct, kind communication is appreciated and reciprocated.

## See Also

- [postures.md](postures.md)
- [disagreement.md](disagreement.md) — when subtext becomes explicit disagreement
- [burnout-awareness.md](burnout-awareness.md) — subtext of overload
