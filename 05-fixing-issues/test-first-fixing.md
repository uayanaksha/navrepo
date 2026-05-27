# Test-First Fixing

A bug fix should start with a **failing test that demonstrates the
bug**. Then make it pass.

This isn't TDD dogma. It's just the most reliable way to fix bugs
without introducing new ones.

## Why

A test-first bugfix gives you:

1. **A precise statement of the bug.** Writing the test forces you to
   articulate what's wrong.
2. **A repro that's deterministic.** The test fails when the bug is
   present.
3. **A regression guard.** The test stays in the suite forever, catching
   future reintroductions.
4. **A fast feedback loop.** No need to manually trigger the bug; just
   run the test.
5. **Reviewer evidence.** The PR shows clearly: "here's the bug, here's
   the fix."

## The Process

### 1. Write the test

```python
def test_user_can_login_with_uppercased_email():
    # Reproduces issue #4521
    create_user(email="alice@example.com", password="pass")
    response = login(email="Alice@example.com", password="pass")
    assert response.status == 200
```

Place it in the relevant test file. The test should *fail* now —
that's the point.

### 2. Run it; confirm it fails

```bash
pytest tests/test_login.py::test_user_can_login_with_uppercased_email
# FAIL
```

A test that already passes isn't testing the bug.

### 3. Fix the code

Now write the minimum fix to make the test pass. Don't refactor
unrelated code; don't add new features.

### 4. Run the test; confirm it passes

```bash
pytest tests/test_login.py::test_user_can_login_with_uppercased_email
# PASS
```

### 5. Run the whole suite

Make sure your fix didn't break anything else.

```bash
pytest
```

### 6. Commit

The test and the fix can go in the same commit, or two commits ("add
failing test for #4521" + "fix #4521"). Either is fine; some maintainers
prefer the split because it makes the test's purpose obvious.

## Test Placement

Where does the test go?

- **Unit-level bug**: in the test file for the affected unit.
- **Cross-unit / integration bug**: in `tests/integration/` or similar.
- **Endpoint-level bug**: in the API test suite.
- **Regression bucket**: some projects have `tests/regressions/` for
  bugs that don't fit other categories.

Match the project's conventions.

## Naming Tests

Bug-driven test names should make the bug obvious:

```
test_login_accepts_uppercased_email
test_order_with_zero_items_returns_error
test_payment_retry_uses_idempotency_key
```

Reference the issue if helpful:

```
test_user_can_login_with_uppercased_email_issue_4521
```

…or in the docstring/comment. The goal: future maintainers see the test
and immediately know it's pinning a specific bug.

## When the Test Is Hard to Write

Sometimes you can't easily write a failing test:

- The bug requires a complex environment.
- The bug is in untested legacy code.
- The bug is concurrency / timing related.

Options:

### Add testability first

Refactor *minimally* so the bug becomes testable:
- Extract the buggy logic into a pure function.
- Mock the I/O so you can call the function directly.
- Inject dependencies.

This is good when the refactor is small. If the refactor would be huge,
defer.

### Write an integration test

If unit tests are blocked, an integration test (slower, but real) may
suffice.

### Write a property test

For concurrency or shape bugs:

```rust
proptest! {
    fn empty_input_doesnt_panic(s in "") {
        let _ = process(&s);
    }
}
```

### Document why a test wasn't added

If you can't add a test, say so in the PR:

> "Manually verified; couldn't write an automated test because [reason].
> Worth tracking the testability improvement in #N."

This is fine occasionally. If you're doing it routinely, the project's
testability is the bug.

## When the Test Was Already There

Sometimes the test already exists but doesn't cover the bug. Add a new
test case or expand an existing one:

```python
@pytest.mark.parametrize("email", [
    "alice@example.com",
    "Alice@example.com",       # added — was missing
    "ALICE@EXAMPLE.COM",       # added
])
def test_login_accepts_email_variants(email):
    create_user(email="alice@example.com", password="pass")
    assert login(email=email, password="pass").status == 200
```

The expansion shows the reviewer exactly what coverage you added.

## Don't Game the Test

Make the test pass by **fixing the bug**, not by tweaking the test:

❌ "The test was wrong; let me change the assertion."
❌ "Let me skip the failing case for now."
❌ "Adding a mock that makes this work."

✅ "The test demonstrates the bug; let me fix the code."

If the test was *legitimately* wrong (asserts incorrect behavior), that
needs its own commit with a clear explanation.

## Tests and Regressions Together

For a high-impact bug, consider also:

- **A property test** if the bug fits a general pattern.
- **An integration test** at the entry point.
- **A manual smoke test** added to a checklist (if the codebase has
  one).

The more places the regression is guarded, the less likely it returns.

## When the Bug Is in Generated Code

If the bug is in generated code (proto stubs, ORM models):

- The fix is usually in the **generator** or the **input schema**.
- The test should run against the generated code's behavior.
- Don't edit generated code by hand — it'll regenerate.

## When the Bug Is in a Dependency

You can't add a test in a dependency you don't own. Options:

- Add a test in your code that **uses** the dep and demonstrates the
  bug. This is an integration test against the dep.
- Propose the test/fix upstream as a PR.
- Use a workaround locally; document it.

See [../02-navigation/dependency-traversal.md](../02-navigation/dependency-traversal.md).

## Test-First as a Habit

After 10 test-first bug fixes, the rhythm becomes natural:

1. See bug → write reproducer test
2. Test fails (confirms bug)
3. Fix code
4. Test passes (confirms fix)
5. Run suite (confirms no regression)
6. Ship

It's slower per PR but much faster over the codebase's lifetime.

## See Also

- [../03-reading-code/tests-as-docs.md](../03-reading-code/tests-as-docs.md) — tests as documentation
- [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md) — the test as MRE
- [pre-push-checklist.md](pre-push-checklist.md) — verifying before sharing
