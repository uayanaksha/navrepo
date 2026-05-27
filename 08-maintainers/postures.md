# Default Postures

The defaults you bring to every maintainer interaction. They sound
obvious until you're stressed, then they're hard.

## The Four Defaults

### 1. Default to patience

Most maintainers are volunteers juggling this with day jobs, families,
and burnout.

- A maintainer not responding for 2 weeks isn't ignoring you. They're
  at their kid's recital.
- A delayed review isn't disrespect. They have 30 PRs to review.
- A short reply isn't rudeness. They wrote it on their phone.

Calibrate your sense of "slow." For most OSS projects:
- A few days = fast.
- A few weeks = normal.
- A month+ = busy, but still OK.

If you can't be patient, OSS contribution may not be the right
investment for you right now.

### 2. Default to assuming good faith

When a maintainer is terse, says no, or seems hostile:

- **First hypothesis**: bandwidth. They had 5 minutes for your PR.
- **Second hypothesis**: language/culture. Non-native English, terse
  norm, fatigue.
- **Third hypothesis**: misunderstanding. They missed context you
  provided.
- **Last hypothesis**: actual hostility.

Most "rude" reviews are exhausted reviews. Reply as if they meant the
charitable version.

### 3. Default to public discussion

DMs fragment context. Future contributors learning from your
interaction need it in public.

- Discuss in PR / issue comments.
- Reference public decisions when relevant.
- If a maintainer DMs you with substantive feedback, ask if you can
  bring it back into the public thread.

Exception: security issues. Those go through the private channel.

### 4. Default to "their call"

It's their project. Your fix is welcome; your veto isn't.

A maintainer can:
- Reject your PR.
- Choose a different direction.
- Close issues you opened.
- Disagree with your design.

This isn't unfair. It's how OSS works.

You can:
- Discuss politely.
- Explain your reasoning.
- Disagree publicly.
- Fork if you really need a different direction.

But you can't *make* them merge.

## Tone Adjustments

### When you don't know the maintainer

Default formal-but-warm:

> "Hi! Thanks for maintaining this project. I noticed [bug]; happy to
> work on a fix if you're interested."

### When you have history

You've contributed before; you can be more casual:

> "Spotted this bug while in `service/orders`; PR up — quick one."

### When you're frustrated

Step away. Reread your draft. Strip emotion. Send only the substance.

```
[draft, never send]
"This has been sitting for 6 weeks. Are you even looking at this?"

[final, send]
"Friendly ping — any chance to take a look this week? Otherwise happy
to close if it's not the right time."
```

### When you're wrong

Acknowledge cleanly:

> "Good catch, you're right — I missed that case. Will update."

Don't over-apologize. Don't explain at length. Fix and move.

## Don'ts

### Don't escalate

If a maintainer says no:
- Don't @-mention other maintainers to overrule.
- Don't ping in unrelated issues.
- Don't repost rejected PRs with different framing.
- Don't take it to Twitter / HN.

These burn future goodwill.

### Don't demand

> "When will this be fixed?"
> "I need this by Friday."
> "Why isn't this a priority?"

These tones assume the maintainer owes you something. They don't.

### Don't compare projects

> "Project Y handles this differently. Why don't you do that?"

Sometimes useful as data; often perceived as critique. Frame as
question, not complaint.

### Don't insist on consistency

> "But you accepted PR #1234 with the same pattern!"

The maintainer is human. They might have made an exception. They might
have changed their mind. They might have been tired.

Move forward, not backward.

## Building Trust Over Time

Trust compounds:

- **First PR**: small, clean, follows process. They learn you're
  competent.
- **Second PR**: solid follow-up. They learn you can be relied on.
- **Tenth PR**: substantive features. They start trusting your
  judgment.
- **Hundredth PR**: you might be a maintainer.

The path is patient, focused contributions. Not heroic single bursts.

## When the Maintainer Is Wrong

Sometimes they are. Genuinely.

Path:
1. **Articulate clearly** what you think is wrong.
2. **Provide evidence.**
3. **Be open to being shown otherwise.**
4. **Accept the outcome.**

If the maintainer remains firm and you're sure they're wrong, your
options are:

- Use a fork.
- Find a different project.
- Wait — maintainers' views shift over years.

You don't win disputes by being right. You win by maintaining
relationship and shipping work.

## Reciprocity

The flip side of patience: when you can, give what you'd want to
receive.

- **Review someone else's PR** in a project where you contribute.
- **Triage an issue.**
- **Update docs.**
- **Help newcomers** in chat.

The longer you stay around a project, the more you become part of its
maintenance. Reciprocity is how communities sustain.

## See Also

- [ping-etiquette.md](ping-etiquette.md)
- [disagreement.md](disagreement.md)
- [reading-between-lines.md](reading-between-lines.md)
- [../12-mindset/](../12-mindset/)
