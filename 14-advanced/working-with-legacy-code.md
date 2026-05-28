# Working with Legacy Code

Legacy code is code without tests you trust, that you're afraid to
change. It's the majority of code in the world, and learning to work in
it safely — rather than wishing it away — is a defining senior skill.

## What "Legacy" Really Means

Michael Feathers' working definition is the useful one: **legacy code is
code without tests.** Not old code, not bad code — *untested* code.
Without tests, you can't change it confidently, because you can't tell
whether your change broke something.

That reframes the whole problem. The goal isn't "rewrite it" — it's "get
it under test so I can change it safely." Everything below serves that.

## The Core Dilemma

To change legacy code safely you want tests. But to *add* tests you
often need to change the code (it's untestable as written — tangled
dependencies, no seams). This chicken-and-egg is the central challenge,
and the techniques below break it.

## Characterization Tests

Before changing legacy code, **pin down what it currently does** — bugs
and all — with characterization tests (a.k.a. "golden master" /
approval tests). You're not asserting what it *should* do; you're
capturing what it *does*, so you'll notice if your change alters it.

```
1. Call the legacy code with representative inputs.
2. Capture the actual output (even if it looks wrong).
3. Assert that output as the expected value.
4. Now you have a safety net: any behavior change shows up as a failure.
```

This lets you refactor with confidence: if the characterization tests
still pass, you haven't changed observable behavior. Once they're in
place, you can *then* fix the bugs deliberately (changing the test to
match the corrected behavior). Approval-testing tools (capture a blob of
output, diff against an approved version) make this cheap for messy
outputs.

## Finding Seams

A **seam** is a place where you can change behavior without editing the
code at that spot — the insertion point for a test. Finding seams is how
you get untestable code under test:

- **Dependency injection seam** — pass a dependency in instead of
  constructing it inside, so a test can pass a fake.
- **Subclass-and-override seam** — override a method in a test subclass
  to break a hard dependency.
- **Link/interface seam** — swap an implementation at the boundary (a
  module, an interface, a build-time substitution).

The smallest possible change to *create a seam* (e.g., extract a method,
parameterize a dependency) is often the safe first step — small enough
to do by hand without tests, enabling the tests that protect the bigger
change.

## The Strangler Fig Pattern

For replacing a large legacy system, don't big-bang rewrite it (see why
in [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)).
Instead, *strangle* it — named after the fig that grows around a tree
and gradually replaces it:

```
1. Put a facade/router in front of the old system.
2. Build new functionality (or rewritten pieces) behind that facade.
3. Route traffic piece-by-piece from old to new.
4. The old system shrinks as the new one grows.
5. Eventually the old system handles nothing; remove it.
```

Each step is small, shippable, and reversible. The system keeps working
the whole time. This beats the doomed "we'll rewrite it from scratch and
switch over" project almost every time — the big-bang rewrite is one of
the most reliable ways to fail.

## Safe Change Tactics

Day-to-day moves in legacy code:

- **Make the change easy, then make the easy change** (Kent Beck). Often
  the safe path is: first refactor (under characterization tests) to
  *make room* for your change, then make the now-simple change.
- **Sprout** — instead of editing a scary method, write your new logic
  in a *new*, tested method/class and call into it from the old code.
  The new code is clean and tested; the old code is barely touched.
- **Wrap** — wrap the old behavior with new behavior (before/after)
  without modifying its guts.
- **Lean on the compiler / types.** In typed languages, a change that
  must ripple will surface as compile errors — a free checklist of every
  affected site. (See [../03-reading-code/types-first.md](../03-reading-code/types-first.md).)
- **Tiny, verifiable steps.** In code you don't trust, make the smallest
  change that can possibly work, verify, repeat. Big confident edits are
  how legacy code bites you.

## Reading Legacy Code

Understanding comes before changing (see
[../03-reading-code/](../03-reading-code/)), but legacy code resists:

- **Use the history.** `git blame` and `git log -S` reveal *why* the
  weird code exists — often a bug fix you'd otherwise reintroduce. This
  is Chesterton's fence in action (see
  [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md)).
- **Tolerate the confusion.** Legacy code is confusing because it
  accreted; sit with it rather than assuming it's just bad (see
  [../03-reading-code/tolerance-for-confusion.md](../03-reading-code/tolerance-for-confusion.md)).
- **Trace one real path** end to end rather than trying to understand
  everything at once.

## Respect Chesterton's Fence

Legacy code is full of fences — odd checks, special cases, workarounds —
that look removable but encode hard-won knowledge (a production
incident, a weird client, a platform quirk). Before deleting "obviously
dead" code:

- Check the history for *why* it's there.
- Add a characterization test that captures the behavior it produces.
- If you still can't find a reason, change it cautiously and watch.

The "obviously useless" code that someone deletes is, with depressing
regularity, the thing that was preventing an outage.

## Anti-Patterns

### The big-bang rewrite

"This is a mess; let's rewrite it from scratch." Rewrites routinely
overrun, reintroduce old bugs, and stall feature work for months.
Strangle incrementally instead.

### Changing without a safety net

Editing untested legacy code freehand and hoping. Add characterization
tests first; then change.

### Deleting code you don't understand

Removing the weird fence because it "looks unnecessary." Find out why it
exists first.

### "Improving" while you're in there

Refactoring unrelated legacy code during a focused change — scope creep
with extra risk because the code is fragile. Stay focused; file the rest
(see [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)).

### Confusing old with bad

Working code that's old isn't legacy if it's tested and understood. Don't
rewrite stable code for aesthetics.

## See Also

- [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md)
- [../03-reading-code/tolerance-for-confusion.md](../03-reading-code/tolerance-for-confusion.md)
- [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)
- [../10-features-refactors/invisible-refactors.md](../10-features-refactors/invisible-refactors.md)
- [../05-fixing-issues/test-first-fixing.md](../05-fixing-issues/test-first-fixing.md)
