# Handling Disagreement

The maintainer says no. Or asks for a change you think is wrong. How
you handle this shapes the relationship more than any successful PR.

## The Frame

Disagreement isn't personal. The maintainer:
- Is making a project decision, not judging you.
- May have context you don't.
- May be wrong; you may be wrong; you both may be partially right.

The goal isn't to win. It's to:
- Reach the best outcome for the code.
- Maintain a relationship that survives this disagreement.

## The Method

### Step 1 — Ask before defending

When you read pushback, your instinct is to defend. Pause. Ask
instead:

> "Help me understand the concern — is it about X, Y, or something
> else?"

Often the disagreement is narrower than the initial comment suggests.
A maintainer saying "this seems wrong" might mean "this specific
edge case worries me" — which is fixable.

### Step 2 — Acknowledge their constraints

Maintainers operate within constraints you may not see:

- Migration plan they haven't published.
- Performance budget.
- Backward compat promise.
- Code review bandwidth.

Acknowledge:

> "I can see this might conflict with the migration in #1230 — I
> didn't realize that was in progress. Should I rebase / wait?"

This signals you're working *with* them.

### Step 3 — Offer alternatives, not justifications

Bad:
> "Here's why my approach is right: [3 paragraphs]"

Better:
> "Here are three approaches I'd consider: A, B, C. A is what I have
> now; B avoids X; C is closer to what you described. Which works
> best?"

Alternatives invite collaboration. Justifications invite argument.

### Step 4 — Know when to fold

If a maintainer says no twice, the answer is no.

- Don't relitigate in the same PR.
- Don't reopen with new framing.
- Don't keep arguing.

Accept. Move forward. If you're sure they're wrong, the long-term
options are:

- Wait — views shift over time.
- Fork — use your version.
- Move on — different project.

You don't have to agree. You do have to accept.

## Disagreement Types

### "I don't think this is the right approach"

The maintainer prefers a different design.

- Ask what shape they'd prefer.
- Try to understand the reasoning.
- Sometimes the disagreement is on something you can adjust.
- Sometimes it's fundamental — then your options are above.

### "We don't want this feature"

The project intentionally won't support what you proposed.

- Accept gracefully.
- If you need it, fork or use a workaround.
- Don't reopen the same proposal with new wording.

### "This won't work because of X"

Technical pushback.

- If X is true: thank them, adapt.
- If X is incorrect: explain politely with evidence.
- Be open to "X is true and I didn't realize" — it's fine.

### "Tests/style/conventions don't match"

Style or process pushback.

- Adjust. Don't argue style.
- If the convention isn't documented, ask: "Is there a style guide I
  missed?" — they may add one.

### "We've discussed this before; outcome was Y"

History pushback.

- Acknowledge the prior decision.
- If you have new info, present it: "I see #1100 closed this. The
  situation has changed because Z — happy to revisit if you'd like."
- If they decline, accept.

## Wording Templates

### When you partly agree

> "I see your point about X. I disagree about Y — wanted to think
> through that with you. Here's why: [brief reasoning]. Curious if
> there's a middle path."

### When you fully agree on reconsideration

> "Good point — I hadn't considered that. Will update."

### When you genuinely don't agree

> "I'd like to push back a little here. My understanding is [X]. If
> [your understanding] is correct, I'm wrong — happy to be corrected.
> If not, we may need to align on how to handle [edge case]. Open to
> either direction."

### When you've been told no twice

> "Understood. Will close this. Thanks for the consideration."

No need to argue further. Accept and move on.

## What Not to Do

### Don't take it personally

Code review is about the code. Skill: hearing "this approach is
wrong" as "this approach is wrong" — not "you are wrong."

### Don't make it adversarial

> "You don't understand what I'm doing."
> "This is obviously the right approach."
> "Why are you blocking this?"

These escalate. Lower the temperature.

### Don't ghost

If you've disagreed and decided to walk away:

❌ Stop responding.
✅ "Going to close — different direction than I had in mind, but I
  understand. Thanks."

Silence after disagreement leaves bad taste. Brief closure is graceful.

### Don't appeal to authority

> "But Senior Engineer agreed this approach is right."
> "But the docs say X."

Sometimes legitimate, but use sparingly. Maintainers can override
docs and other engineers' opinions on their project.

### Don't take it public

If a disagreement is going badly, *don't* take it to:
- Twitter / Mastodon / Bluesky
- Hacker News
- A blog post

Even if you're right. Even if you're frustrated. The reputational
cost to you is worse than the cost to them.

## When You're Wrong

Acknowledge cleanly:

> "Hmm, you're right — that case I didn't think about. Updating."

Don't:

- Over-apologize.
- Explain at length why you missed it.
- Delete your previous comments.

A short acknowledgment is professional. Move on.

## When You're Right But It Doesn't Matter

Sometimes you're technically right but the maintainer is choosing not
to engage. That's still their call.

Examples:
- "Your approach is cleaner, but I'd rather not change this area
  right now."
- "Yes, but our convention is X; we'd rather stay consistent."

Acceptable responses. You can:
- Comply.
- Withdraw the PR.
- Note disagreement and move on.

You can't force engagement.

## Disagreement That Resolves Well

Best-case path:

1. PR submitted.
2. Maintainer requests change.
3. Contributor asks clarifying question.
4. Maintainer explains constraint.
5. Contributor offers two alternatives.
6. Maintainer picks one.
7. Contributor updates.
8. Merge.

This is a normal review cycle. Don't aim to avoid disagreement — aim
to handle it well.

## Disagreement Across Cultures

OSS is global. Communication norms vary:

- Some cultures default to indirect. "Maybe we should consider..."
  may mean "no."
- Some default to direct. "This is wrong" is a normal review comment,
  not aggression.
- English is many maintainers' second language. Brevity may be
  fluency, not rudeness.

When in doubt, assume good faith and ask clarifying questions.

## Long-Term Disagreement Pattern

If you consistently disagree with one maintainer:

- Maybe you're not aligned with the project's direction.
- Maybe their style isn't compatible with yours.
- Maybe the project has a culture you don't fit.

That's OK. Find a project that fits better. Maintainers and
contributors self-select for similar reasons.

## See Also

- [postures.md](postures.md) — the baseline tone
- [reading-between-lines.md](reading-between-lines.md) — what's actually meant
- [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md) — the internal work
