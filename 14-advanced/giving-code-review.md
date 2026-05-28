# Giving Code Review

Reviewing well is a distinct skill from coding well. A good review
catches real problems, teaches, and unblocks — without drowning the
author in noise or grinding the change to a halt. This is the other side
of [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md).

## What Review Is For

A review has a few real jobs, in priority order:

1. **Catch correctness, security, and data-safety problems.** The
   non-negotiables.
2. **Ensure maintainability** — will the next person understand this?
3. **Check it fits** the project's direction and conventions.
4. **Share knowledge** — both ways; you learn the change, the author
   learns from your feedback.

It is *not* for: rewriting the code the way you'd have written it,
showing off, or enforcing personal taste as law.

## Review the Description Before the Diff

Start with *what the author was trying to do*, not line one of the diff:

1. **Read the PR description.** What problem, what approach, what's out
   of scope. If it's empty, your first request is a description — you
   can't review intent you don't know.
2. **Read the linked issue** for the original motivation.
3. **Read the tests** — they tell you what the author thinks the change
   does and considers the contract.
4. **Then read the implementation**, now that you know the intent.

Reviewing the diff cold, top to bottom, is the slow and error-prone
path. (Mechanics for pulling the PR local are in
[../11-tooling/pr-reviewing-tools.md](../11-tooling/pr-reviewing-tools.md).)

## Nits vs. Blockers — Label Everything

The single most useful habit: **mark the severity of every comment** so
the author knows what must change versus what's optional.

| Label | Meaning | Author's obligation |
|---|---|---|
| **blocker** | Must fix before merge (bug, security, broken contract) | Required |
| **(nothing) / "consider"** | A real suggestion, author's call | Discretionary |
| **nit:** | Trivial/cosmetic preference | Take it or leave it |
| **question:** | You want to understand, not change | Answer, maybe no change |
| **praise:** | This is good — say so | None; morale |

A review that's all unlabeled comments forces the author to guess what's
mandatory. A review where the only blocker is buried among ten nits
hides the thing that matters. Label, and put the blockers up front.

Use `nit:` liberally and honestly — it lets you mention a preference
*without* holding the PR hostage to it, which defuses bikeshedding (see
[../12-mindset/bikeshedding.md](../12-mindset/bikeshedding.md)).

## Suggest Alternatives, Don't Just Reject

"This is wrong" leaves the author stuck. "This is wrong *because X*;
consider Y" moves them forward. Always pair a problem with a direction:

- Explain the *why*, not just the *what* — the author learns, and can
  apply it to edge cases you didn't mention.
- For concrete small fixes, use **suggestion blocks** (the
  one-click-apply kind) — faster than describing the change in prose.
- For bigger concerns, propose the approach and discuss, don't dictate
  the exact code.

## Tone: You're on the Same Side

Written review strips tone; default to warmth and explicitness:

- **Critique the code, not the person.** "This function has a race"
  not "you wrote a race." (The author's side of this is in
  [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md).)
- **Ask, don't accuse.** "What happens if this is null?" beats "this
  will crash on null."
- **Praise genuinely.** Note the clever bits and the good tests.
  Reviews that are all criticism are demoralizing and train people to
  fear review.
- **Assume competence and good faith.** They probably had a reason;
  ask before assuming a mistake.
- **Be specific.** Vague disapproval ("this feels off") is unactionable
  and frustrating. Point to the line and the concern.

## Don't Be the Bottleneck

A review nobody can act on, or that never arrives, is its own failure:

- **Be timely.** A fast "I'll review tomorrow" beats silence. A PR
  blocked on your review for a week is a real cost (and across time
  zones, longer — see [../13-hidden-knowledge/time-zones.md](../13-hidden-knowledge/time-zones.md)).
- **Be decisive.** Approve, request changes, or comment — don't leave it
  in limbo. "Approve with nits" ("LGTM, optional nits inline") unblocks
  the author for trivial leftovers.
- **Scope your review to the PR.** Don't demand the author also fix
  unrelated nearby issues — that's scope creep you're imposing (file a
  follow-up instead). See
  [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md).
- **Know when to take it offline.** If a thread exceeds a few
  round-trips, a quick call or pairing session beats a comment war.

## Calibrate to the Author and the Change

Review isn't one-size-fits-all:

- **New contributor:** more teaching, more encouragement, more patience.
  Their first PR sets whether they come back.
- **Experienced teammate:** terser is fine; they want the signal, not
  the hand-holding.
- **Risky change** (migration, security, core path): deeper scrutiny,
  run it locally, maybe a second reviewer.
- **Low-risk change** (docs, a small fix): don't over-review; approve
  and move on.

Spending equal effort on every PR means under-reviewing the dangerous
ones and over-reviewing the trivial ones.

## Approving: What Your Signature Means

An approval says "I believe this is correct and ready." It's a
signature, not a formality:

- Don't approve what you don't understand. "I didn't really get the
  concurrency part" means ask, or defer to someone who does.
- Don't rubber-stamp to be nice. A waved-through bug helps no one.
- Approving with trust is fine for a known author on a low-risk change —
  calibrate, don't perform.

## Anti-Patterns

### The drive-by rewrite

Rewriting the PR in comments to match exactly how you'd have done it.
Unless their version is *wrong*, "different from mine" isn't a reason.

### Nit-bombing

Twenty cosmetic comments and no engagement with the actual design.
Automate the cosmetic stuff (formatter/linter) and spend attention on
substance.

### Vague disapproval

"This doesn't feel right" with no specifics. Unactionable. Find the
concrete concern or don't raise it.

### The silent veto / ghosting

Sitting on a PR without approving or explaining. Blocks the author
indefinitely and is worse than a clear "no." Respond.

### Unlabeled severity

A wall of comments where the author can't tell the blocker from the
nit. Mark severity, lead with blockers.

### Reviewing the author, not the code

Comments about the person ("you always do this") instead of the change.
Keep it about the code, always.

## See Also

- [reviewing-large-prs.md](reviewing-large-prs.md)
- [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)
- [../12-mindset/bikeshedding.md](../12-mindset/bikeshedding.md)
- [../11-tooling/pr-reviewing-tools.md](../11-tooling/pr-reviewing-tools.md)
- [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)
