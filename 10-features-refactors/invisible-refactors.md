# Invisible Refactors

A clean refactor is **invisible from outside**. Same inputs, same
outputs, same observable behavior. Different internals.

## The Test

After the refactor:

- [ ] All tests pass without modification.
- [ ] Public API unchanged.
- [ ] No new behavior, no removed behavior.
- [ ] Externally observable side effects identical.
- [ ] Performance characteristics roughly unchanged.

If any of these are no, the refactor isn't pure — it's a behavior
change disguised as a refactor.

## Why It Matters

A pure refactor:

- Is reviewable in minutes (verify "no behavior change").
- Has minimal regression risk (existing tests pin behavior).
- Doesn't need extra docs / migration notes.
- Doesn't break clients.

A mixed refactor:

- Needs detailed review.
- Has higher risk.
- May need a heads-up to users.
- Confuses git bisect later.

## What Pure Refactors Look Like

### Extract function

```python
# before
def process(o):
    # 50 lines doing stuff

# after
def process(o):
    validated = _validate(o)
    enriched = _enrich(validated)
    return _execute(enriched)

def _validate(o): ...
def _enrich(o): ...
def _execute(o): ...
```

Behavior identical. Structure clearer.

### Rename

```python
# before
def proc_o(obj): ...

# after
def process_order(order): ...
```

Public API stays compatible (with deprecation wrapper if needed).

### Move

```
internal/foo.go    →    internal/processing/foo.go
```

Imports updated; behavior same.

### Reorganize

Different grouping, same code:

```python
# before: all in one file
# after: split into types.py, validators.py, processors.py
```

### Extract type / interface

```rust
// before
pub fn handle(req: HttpRequest, db: &Database) -> HttpResponse { ... }

// after
pub fn handle<S: Storage>(req: HttpRequest, db: &S) -> HttpResponse { ... }
```

The function does the same thing; just accepts an abstraction.

### Performance refactor

```python
# before: O(n²) nested loop
# after: O(n) with a dict

# Same outputs, faster.
```

If outputs are exactly the same, it's a refactor. If outputs differ
(rounding, ordering), it's behavior change.

## What Refactors Are NOT

### Refactor + bug fix

```python
# "while I'm refactoring, fix this edge case"
```

Split. Bug fix is its own PR.

### Refactor + new feature

```python
# "while refactoring, add support for X"
```

Split. Refactor first; feature follows.

### Refactor + style change

```python
# "while refactoring, also rename half the variables"
```

Often OK if the renames are part of the refactor. But for big rename
churn, split.

### Refactor + library upgrade

```
# "while refactoring auth, also bump auth lib to v2"
```

Definitely split. Library upgrade has its own risks.

## How to Verify "Invisible"

### 1. Pre-refactor: ensure tests cover behavior

If tests are weak, the refactor isn't safe. Either:

- Add tests for the current behavior first.
- Postpone the refactor.

A refactor without test coverage is just "rewriting." Risky.

### 2. Make the change

Move methodically. One change at a time:

- Rename in one commit.
- Extract in another.
- Move in another.

This lets you bisect if something breaks.

### 3. Run tests after each change

If tests fail, you broke something. Investigate before continuing.

### 4. Check for behavior leaks

Beyond tests:
- Manual smoke test (run the actual app).
- Performance benchmark if applicable.
- Linter / type checker.

### 5. Diff inspection

Read the diff. For each chunk, ask: "is this purely structural, or did
behavior shift?"

## Refactor in Tiny Steps

For larger refactors, use the **expand-contract** pattern:

### 1. Expand

Add the new structure alongside the old.

```python
# old function
def process(o): ...

# new function
def process_v2(o):
    return new_impl(o)
```

Both exist. Old still works.

### 2. Migrate

Switch callers one at a time:

```python
# was:
result = process(order)
# now:
result = process_v2(order)
```

Each caller is a small reviewable change.

### 3. Contract

When all callers are switched, remove the old:

```python
# delete old process(); keep process_v2
# (or rename process_v2 to process)
```

Each phase is independently reviewable. Risk distributed.

## Refactor PRs

A pure refactor PR description:

```markdown
## Summary

Extract OrderValidator from inline handler logic. **No behavior
change.**

## Why

Preparing for #1500 (new order types) — need a single point to add
validation rules.

## Changes

- New file `validators/order.go`.
- Inline validation in `handlers/orders.go:31-58` extracted to
  `OrderValidator.Validate()`.
- Existing tests pass unchanged.

## Verification

- [x] All tests pass (no modifications)
- [x] Diff is purely structural (verified by self-review)
- [x] Manual smoke test against staging
```

The "no behavior change" claim and verification are central.

## Refactors and Tests

Tests should generally not change in a pure refactor. Exceptions:

### Test was wrong

The pre-existing test asserted incorrect behavior; refactor surfaces it.

→ Split: separate PR fixes the test.

### Test was at the wrong level

Refactor changed internal structure; some unit tests are now testing
something that doesn't exist as a unit anymore.

→ Move tests to the new level (often integration). Note in PR.

### Test gets simpler

Refactor made an awkward setup unnecessary; test gets cleaner.

→ Update the test. Note in PR. Verify it still asserts the same
behavior at a high level.

## When Refactor Is Visible

Sometimes refactors are *partially* visible, e.g.:

- New module structure → import paths change.
- Public API renamed → users must update.
- Removed deprecated feature → users must migrate.

These need:

- Migration notes in CHANGELOG.
- Deprecation period if possible.
- Communication to users.

See [deprecation.md](deprecation.md) and [migration-strategies.md](migration-strategies.md).

## When to Refactor

Most useful:

- **Before adding a feature** that would be awkward in current code.
- **After a bug** revealed structural issues.
- **As part of regular hygiene** — small ongoing improvements.

Less useful:

- **For its own sake.** Refactoring without a goal can be busywork.
- **In code you don't understand.** You'll likely break something.
- **In code you don't own.** Maintainer may not want changes.

## Anti-Patterns

### "Big bang" refactor

A 5000-line refactor in one PR. Won't merge. Won't be reviewed
properly. If it does land, regressions are inevitable.

### Refactor without tests

If you can't verify behavior is preserved, you're guessing.

### Drive-by refactor in bugfix PR

The 10-line bugfix becomes a 200-line PR because you "improved" the
surrounding code. Split.

### Refactor that "improves" naming

Renames have value but are also disruptive. Bundle with other
refactors if doing many; or do separately if it's just a rename.

### Mixed surface

The PR has 100 lines of refactor + 50 lines of new logic. Reviewer
can't tell which is which.

## See Also

- [scope-discipline.md](scope-discipline.md)
- [migration-strategies.md](migration-strategies.md)
- [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md)
- [../14-advanced/working-with-legacy-code.md](../14-advanced/working-with-legacy-code.md)
