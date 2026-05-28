# PR Discussion Phrases

The actual words to use in PR and issue back-and-forth. Communication is
a skill; having good phrasing ready keeps you calm, clear, and easy to
work with. The reasoning is in [../08-maintainers/](../08-maintainers/)
and [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md).

> Adapt these to your voice — copied robotically they sound canned. The
> point is the *shape* of a good response.

## Pushing Back Gently

When you disagree but want to keep it collaborative:

- "I went with X because of Y — does that hold up, or am I missing
  something?"
- "I see the concern. My worry with that approach is Z. Could we...?"
- "That's fair. One tradeoff: [X]. Happy to go either way — what's your
  call?"
- "Could you say more about the concern here? I want to make sure I
  address the real issue."
- "I think there might be a case this misses: [concrete scenario]. Worth
  handling?"

Lead with reasoning, stay curious, and make it easy for them to respond.

## Conceding Gracefully

When they're right, or it's their call:

- "Good catch — fixed."
- "You're right, I hadn't considered that. Updated."
- "Makes sense, done."
- "Fair enough — your call on this one. Changed it."
- "Agreed, that's cleaner. Thanks."

Concede cleanly and without groveling. "Good catch, fixed" is the ideal
register (see [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)).

## When You Need Time

Don't reply in the heat of the moment (the one-hour rule):

- "Need to think about this one — I'll come back to it tomorrow."
- "Good question; let me dig into it and get back to you."
- "Want to test a couple of approaches before I respond properly."

Signaling you're engaged but considering beats both a rushed reply and
silence.

## Expressing Uncertainty (a Strength)

Calibrated confidence builds trust (see
[../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md)):

- "I'm fairly sure about X, but not confident on the concurrent case —
  could someone who knows this area check?"
- "I *think* this is right; I haven't been able to test [specific
  scenario]."
- "Not sure this is the right approach — open to alternatives."
- "Guessing here, but: [hypothesis]. Can anyone confirm?"

Labeling your confidence level makes your *confident* statements more
trustworthy.

## Asking for Review / Following Up

Patiently, without nagging (see
[../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md)):

- (First contact) "This is ready for review when someone has time — no
  rush."
- (After a real interval) "Gentle bump on this — let me know if anything's
  blocking or if I should change the approach."
- "Is there anything I can do to make this easier to review?"
- "No urgency — just flagging this is still open whenever you get a
  chance."

One polite follow-up after a genuine wait, never daily.

## Scoping / Declining Scope Creep

Keeping the PR focused (see
[../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)):

- "Good idea — I'd rather do that in a separate PR to keep this one
  reviewable. Filed #N, tagging you."
- "That's a bigger change than this PR's scope; want me to open a
  follow-up issue?"
- "Including the trivial fix here; the larger refactor I'll do
  separately."

## Withdrawing / Accepting a No

Gracefully closing your own PR or accepting a decline (see
[../13-hidden-knowledge/saying-no.md](../13-hidden-knowledge/saying-no.md)):

- "Totally understand — happy to close this if it doesn't fit the
  direction."
- "Makes sense, thanks for considering it. I'll close this out."
- "Fair call. I'll maintain this as a patch/plugin on my end."
- "Appreciate you taking the time to look. Closing."

How you take a "no" is remembered — close the loop warmly.

## As a Reviewer: Giving Feedback

Labeling severity and staying kind (see
[../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)):

- "**blocker:** this will crash on null input — needs a guard."
- "**nit (non-blocking):** I'd name this `count`, but up to you."
- "**question:** what happens here if the list is empty?"
- "**praise:** nice, this is much clearer than before."
- "Consider [X] — not required, just a thought."
- "What do you think about [alternative]? Either's fine with me."

## When Things Get Tense

De-escalating (see [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)):

- "I think we both want the same thing here — let me re-read your point."
- "Happy to hop on a quick call if that's easier than going back and
  forth."
- "You may be right; I'd still lean toward [X], but I'll defer to you on
  this."
- "Let's not let this block the rest — can we park this thread and
  resolve the others?"

## Thanking

Cheap, rare, and powerful (see
[../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)):

- "Thanks for the careful review — caught a couple things I'd have
  missed."
- "Appreciate the quick turnaround."
- "Thanks for the context, that helps a lot."

## See Also

- [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)
- [../08-maintainers/reading-between-lines.md](../08-maintainers/reading-between-lines.md)
- [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)
- [../13-hidden-knowledge/saying-no.md](../13-hidden-knowledge/saying-no.md)
