# Receiving Review

Code review feels personal because you made the thing. It isn't. The
single most valuable professional habit is separating *the critique of
your code* from *a judgment of you*. Get this right and review becomes
free mentorship instead of a threat.

## The Core Reframe

The reviewer is not your opponent. You are both on the same side, and
the opponent is *bugs reaching users*. A reviewer who finds a flaw in
your PR just did you a favor — that flaw was going to ship with your
name on it.

> Review is about the code, not about you.

Internalizing this turns the emotional charge of "they're attacking my
work" into "we're improving our work." Same comments, completely
different experience.

## Acknowledge Before You Defend

The instinct when you read a critical comment is to explain why you're
right. Resist the first impulse. The order that works:

1. **Understand** what they're actually saying. Re-read it. Assume
   they have a point.
2. **Acknowledge** it. "Good catch." "You're right." "I hadn't
   considered that."
3. *Then*, if you still disagree, explain — with reasoning, not
   defensiveness.

Acknowledging first costs you nothing and changes the whole tone. It
signals you're collaborating, not protecting territory. Even when you
end up disagreeing, you've disagreed from a place of having listened.

## The One-Hour Rule

When a comment lands and you feel the heat rise — defensiveness,
embarrassment, irritation — **do not reply yet.** Write the angry reply
in a text file if you must, then delete it. Wait an hour. Often longer.

Almost every reply sent while flushed makes things worse. After an
hour:

- The comment usually looks more reasonable than it did.
- You can see the technical content under the tone.
- Your reply will be about the code, not your bruised ego.

Nobody has ever regretted waiting an hour. Plenty regret the reply they
fired off in the first sixty seconds.

## Tone Is Lossy in Text

Written review strips away tone of voice and facial expression. A
terse "why?" reads as hostile but usually just means "why?" A comment
with no smiley isn't cold; it's efficient.

Assume good faith and the most generous reading. The reviewer is
typically busy, helping you for free, and not trying to wound you. If a
comment genuinely reads as hostile, the move is still calm: "Happy to
change this — can you say more about the concern?"

See [../08-maintainers/reading-between-lines.md](../08-maintainers/reading-between-lines.md)
for decoding terse maintainer feedback.

## Separating Signal from Preference

Not every comment is equal. Sort them:

| Type | Response |
|---|---|
| Correctness bug | Fix it. They caught something real. |
| Security / data issue | Fix it, thank them loudly. |
| Design concern | Engage seriously; discuss tradeoffs. |
| Convention / consistency | Match the project; it's their house. |
| Nit / preference | Take it or push back lightly; low stakes. |
| Bikeshed | Redirect (see [bikeshedding.md](bikeshedding.md)). |

You don't have to *agree* with everything. You do have to *consider*
everything and respond respectfully.

## When You Disagree

Disagreement is fine and often valuable. Do it well:

- **Lead with the reasoning, not the verdict.** "I went with X because
  of Y constraint — does that hold up, or am I missing something?"
- **Stay curious, not certain.** You might be the one who's wrong.
- **Defer on their turf.** In *their* project, conventions and
  direction are theirs to set. You can make the case, but the call is
  theirs. See [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md).
- **Know when to concede.** Past a point, "you may be right, but I'd
  like to go with this for now" or simply conceding keeps the
  relationship healthy. Winning every argument loses the war.

## Don't Over-Apologize Either

The flip side of defensiveness is groveling. "I'm so sorry, that was so
stupid of me" makes review awkward and centers your feelings instead of
the code. A simple "good catch, fixed" is the right register.
Mistakes in code are normal and expected — that's *why* review exists.

## Thank Your Reviewers

A reviewer spent their time improving your work for free. "Thanks for
the careful review" costs nothing and compounds: people review more
carefully, and more willingly, for those who appreciate it. This is
direct relationship capital (see
[../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)).

## Anti-Patterns

### Replying in the heat of the moment

The single most common way good engineers damage relationships. Wait
the hour.

### Defending every line

Treating each comment as something to win turns review into combat.
Reviewers stop bothering, and your code stops improving.

### Taking it personally and going quiet

Disappearing from your own PR because a comment stung leaves it
stranded and signals you can't take feedback. Engage, even if it's just
"need to think about this, back tomorrow."

### Arguing about their project's conventions

In someone else's repo, "this isn't how I'd do it" loses to "this is
how we do it here." Match the house style.

### Treating LGTM as the goal

The goal is good code, not approval. If a reviewer waves through
something you know is shaky, that's not a win.

## See Also

- [bikeshedding.md](bikeshedding.md)
- [imposter-syndrome.md](imposter-syndrome.md)
- [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)
- [../08-maintainers/reading-between-lines.md](../08-maintainers/reading-between-lines.md)
- [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)
