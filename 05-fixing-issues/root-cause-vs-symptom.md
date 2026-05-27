# Root Cause vs Symptom

The most consequential decision in fixing a bug is **where** to fix it.
Symptoms are local and visible. Root causes are deeper and load-bearing.

Symptom fixes accumulate. Each one makes the codebase a little worse.
Eventually, the project is held together by them.

## The Distinction

A **symptom** is the observed wrong behavior:
- "Login returns 500 when email contains uppercase."

A **root cause** is why:
- "The user-lookup function is case-sensitive, but emails are stored
  lowercased."

A **symptom fix** patches the visible wrongness:
- Catch the 500 in the login handler; return 401 instead.

A **root cause fix** addresses the underlying problem:
- Normalize email case in the lookup function (or at the storage
  boundary).

A symptom fix is faster. A root cause fix is correct.

## Why Symptom Fixes Are Tempting

- **Faster to ship.** Smaller diff, less testing.
- **Less risky.** You don't touch shared code.
- **Easier to review.** Maintainer accepts quickly.
- **Often "good enough"** for the immediate problem.

The cost is invisible: the same bug pattern will recur in other code
paths that touch the same root cause.

## When to Fix the Symptom

Sometimes symptom fixes are correct:

1. **The root cause is out of scope.** A bugfix shouldn't refactor the
   architecture. Patch the symptom, file an issue for the root cause.

2. **The root cause is being fixed elsewhere.** Don't duplicate work.
   Use a symptom fix to unblock.

3. **The root cause is in code you don't own.** Upstream library bug —
   workaround locally, propose upstream fix separately.

4. **The risk of root-cause fix exceeds the benefit.** Rarely true, but
   sometimes the underlying code is too fragile to touch.

In all these cases: **say so in the PR.** "Patching the symptom because
X; tracked separately in #N."

## When to Fix the Root Cause

Most of the time, the root cause fix is the right one:

1. **The root cause is in scope.** It's the same module, same domain.
2. **The same symptom appears in multiple call sites.** A single
   root-cause fix patches them all.
3. **The fix is local enough to not be a refactor.** A few lines, not
   a rewrite.
4. **You can write a test that pins the behavior.** Then the fix is
   safe.

## The Layer Decision

When the same bug can be fixed at multiple layers:

```
User input  →  Handler  →  Service  →  Repository  →  DB
```

A case-sensitivity bug could be fixed at:
- **User input layer**: validation rejects uppercase.
- **Handler**: lowercase before passing in.
- **Service**: lowercase as first action.
- **Repository**: lowercase before query.
- **DB**: case-insensitive column collation.

Choose based on:

- **Where the invariant should hold.** Usually at the *boundary* — once
  data passes a certain layer, downstream can trust it.
- **What else uses this code.** If the repository is also used by an
  API endpoint, fixing only the handler leaves the API broken.
- **What's idiomatic in the codebase.** Match existing patterns.

In the email example: fixing at the storage boundary (or even DB level)
ensures every caller sees consistent behavior. Fixing only at the
login handler leaves password-reset, signup, etc. still buggy.

## "Defense in Depth" — Multiple Layers

Sometimes both/and:

- Normalize at the boundary (root cause fix).
- *Also* validate at the input layer (defense against future bugs).

This is fine when the defense is cheap and adds clarity. It's not fine
when "defense in depth" becomes a pile of redundant checks.

## Finding the Root Cause

Process, beyond "guess and patch":

### 1. Reproduce in isolation

See [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md).
A minimal repro reduces noise.

### 2. Trace the failing path

Walk the code from input to failure. Note each step where the data
could be wrong but isn't checked.

### 3. Ask "where should this have failed earlier?"

If a null reaches your code, was it null when it entered? If a string
has the wrong case, where was it last "right"?

### 4. Ask "why" multiple times

See [five-whys.md](five-whys.md).

### 5. Look for related bugs

If you found a root cause, **search for other symptoms**:

```bash
# Found that emails should be lowercased; search for case-sensitive lookups:
rg 'WHERE email = ' --type sql
rg 'find_by_email\(' --type rust
```

Often one root cause fix collapses 3 bugs.

## When the Root Cause Is Architectural

Sometimes "the bug" is actually "the design is wrong." Don't fix this
in a bugfix PR.

Path:
1. Patch the symptom or add a defensive layer.
2. Open an issue describing the architectural problem.
3. Propose the refactor separately, with discussion.

See [../10-features-refactors/](../10-features-refactors/).

## "Fix the Code, Not the Test"

A common anti-pattern: a test fails, so you change the test to pass.

Symptoms of this:
- Adjusting `assertEquals(5, x)` to `assertEquals(6, x)` without
  understanding why x changed.
- Changing test expected values to match current behavior.
- Adding `if x == old_buggy_value: x = correct_value` in tests.

The test was right. The code is wrong. Fix the code.

Exception: when the test was wrong to begin with (asserts current
behavior instead of intended behavior). Then yes, fix the test — with
a clear PR explanation.

## When You're Not Sure

If you can't tell whether something is a symptom or root cause:

- **Read related code.** Does the same logic appear elsewhere?
- **Look at history.** When was this code added? Did it fix a similar
  bug?
- **Ask a maintainer.** "I'm thinking of fixing this at layer X — is
  there a reason it should be at Y?"

A 5-minute clarifying question saves a 5-week back-and-forth review.

## Anti-Patterns

- **Try/except all the things.** Catching errors to make them disappear,
  not handle them.
- **Cosmetic null checks.** Adding `if x is not None` everywhere
  instead of fixing why x was None.
- **String replace fixes.** "Replace 'foo' with 'bar' in this one
  place" when the same string appears in 12 others.
- **One-off conditionals.** `if user_id == "buggy-user-123": skip()`.
  Looks unbelievable; happens routinely.

## The Test of a Good Fix

A good fix means:

- The exact reported bug doesn't happen.
- Similar bugs (same root cause) don't happen.
- Adding the bug back would be obviously wrong to a reviewer.
- A test exists that would catch the regression.
- No new code paths or hidden state were introduced.

## See Also

- [five-whys.md](five-whys.md) — the drilling protocol
- [fix-surface-area.md](fix-surface-area.md) — sizing the fix
- [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md) — when "weird code" is the previous fix
