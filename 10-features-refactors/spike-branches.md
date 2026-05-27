# Spike Branches

A spike is a **throwaway prototype**. Its output is a learning, not
code.

## The Idea

Before committing to a design, ship a quick & dirty version that
proves the approach works (or doesn't). The code is meant to be
discarded.

Why bother:

- Discovers unknowns fast.
- Surfaces hidden costs before deep investment.
- Generates evidence for proposals.

Why it's hard:

- Resisting the urge to polish.
- Throwing away "working" code.

## When to Spike

- **New approach you've never tried.** Build it crudely once.
- **Library evaluation.** Will this lib actually fit?
- **Performance question.** Is this approach fast enough?
- **API exploration.** What does this shape feel like to use?

Don't spike when:

- The approach is clear and proven.
- A 10-minute discussion would settle it.
- You have a real PR ready.

## How to Spike Well

### 1. Time-box

Pick a duration: 1 hour, half-day, day. **Stop when time's up**, even
if "almost done."

Time-boxing is the discipline. Without it, spikes become "the actual
implementation, just rough."

### 2. Name the branch clearly

```
spike/oauth-pkce-feasibility
spike/postgres-vs-sqlite
spike/new-api-shape
```

`spike/` prefix signals "throwaway."

### 3. Cut corners aggressively

- No error handling.
- No tests.
- Hardcoded values.
- Copy-pasted scaffolding.
- Print statements instead of logging.

The point isn't quality. The point is information.

### 4. Note what you learn

Keep notes in a scratchpad or even at the bottom of a file:

```
SPIKE NOTES (oauth-pkce):
- The lib's `pkce` module works fine.
- Token refresh path is unclear; might need custom handling.
- Performance is OK (~50ms for token exchange).
- Estimated real implementation: 2-3 days.
```

These notes are the output. The code is the input that produced them.

### 5. Throw it away

When the spike concludes:

- Capture findings (above).
- Delete the branch (or archive separately).
- Implement properly from scratch.

Don't:
- Reuse spike code in production.
- Slowly polish the spike into the real thing.

The spike's design was guided by "make it work fast." The real
implementation's design should be guided by "make it work right."

## Why Not Just Polish

A common temptation: "the spike works; why throw it away?"

Reasons:

- **Skipped tests.** Adding them retroactively is harder than from
  scratch.
- **Skipped error handling.** Retrofit is sloppy.
- **Hardcoded coupling.** Spike code skips abstractions; production
  code needs them.
- **Implicit constraints.** Spikes have assumptions you didn't make
  explicit; in production code, you'd surface them.

The spike's value is *learning*, not *code*. Retain the learning.

## Spike Output: Three Forms

### A go/no-go decision

"Approach X works." Or: "Approach X doesn't work, here's why."

### A revised proposal

The spike taught you something. Update the proposal:

> Initial proposal said Option A; spike found that A has problem Y.
> Revising to A' which handles Y by [solution]. Updated cost estimate.

### A finding ("don't bother")

"Spent half a day. The library doesn't support what we need. Need
to fork or use Z instead."

Negative results are also valuable.

## When the Spike Becomes a Problem

Sometimes you can't easily throw away a spike:

- It already has business value (users want it).
- Rewriting would lose context.
- Politics ("you spent two weeks on that; you can't throw it away").

These are project anti-patterns. Spikes are *meant* to be thrown away.
If your culture punishes that, the practice fails.

Mitigation:

- Be explicit upfront: "this is a spike; it'll be thrown away."
- Get manager / maintainer buy-in.
- If it becomes "real," at least pay the refactor cost openly.

## Spike vs Draft PR

Both can serve "see if this works." Differences:

| Spike | Draft PR |
|---|---|
| Throwaway | Will eventually merge |
| Cuts every corner | Cleanish |
| Private (no PR) | Shared |
| For your learning | For team discussion |

Sometimes you spike first (private), then draft PR (shared). Or one
or the other.

## Multiple Spikes

For a complex decision, run two spikes:

- Spike A: Approach 1.
- Spike B: Approach 2.

Compare. Pick. Implement the winner from scratch.

This is a few hours of "wasted" work that saves weeks of building
the wrong thing.

## Spikes for Learning

You can spike to learn:

- "Spike to understand how cargo workspaces interact with feature
  flags."
- "Spike to compare three async runtime implementations."

This blurs into [build-a-toy](../09-unknown-tech/build-a-toy.md) territory. Both are
deliberate temporary-code-for-learning practices.

## Spike Branches in Teams

If others might see your spike branch:

- Mark clearly in README at top of file: "Spike — throwaway. See #N
  for the real implementation."
- Don't request review.
- Don't leave it around for months; clean up.

A `spike/` branch sitting in remote for a year creates confusion.
Delete after use.

## Spike-Driven Refactors

For risky refactors:

- Spike the refactor (don't keep the code).
- Note: what broke? What needed special handling?
- Now write the real refactor with those insights.

Often the spike reveals the refactor was easier or harder than
expected.

## See Also

- [proposals-and-rfcs.md](proposals-and-rfcs.md) — when spike informs a proposal
- [../09-unknown-tech/build-a-toy.md](../09-unknown-tech/build-a-toy.md)
- [invisible-refactors.md](invisible-refactors.md)
