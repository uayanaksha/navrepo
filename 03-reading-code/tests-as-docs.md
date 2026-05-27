# Tests As Documentation

Tests are the most consistently-updated documentation in a codebase. The
README can go stale; comments can lie; tests run on every change and
*must* be accurate or CI fails.

When approaching a new module, **read its tests first**.

## Why Tests Beat Docs

| Comments | Tests |
|---|---|
| Drift from code | Fail when wrong |
| Often written once | Run on every change |
| Describe intent | Demonstrate behavior |
| May omit edge cases | Often *only* exist for edge cases |
| Linear text | Executable, debuggable |

Comments tell you what someone *thought* the code did. Tests tell you
what the code *actually does* — at least for the cases the tester thought
of.

## Reading Tests Strategically

### Find them

Common layouts:

- **Co-located**: `foo.go` + `foo_test.go` (Go), `Foo.kt` + `FooTest.kt` (Java/Kotlin)
- **Mirrored tree**: `src/foo.py` + `tests/test_foo.py`
- **Separate dirs**: `lib/foo.rb` + `spec/foo_spec.rb` (Ruby/RSpec)
- **Embedded** (Rust): `#[cfg(test)] mod tests` at end of `foo.rs`

If unsure: `find . -path '*test*' -name '*foo*'` or grep for `import.*foo` near `test`.

### Skim test names first

Test file structure usually mirrors the module's API. Just reading test
*names* often gives you the module's behavior in 30 seconds:

```
TestProcessOrder_HappyPath
TestProcessOrder_EmptyOrder_ReturnsError
TestProcessOrder_OutOfStock_RefundsCustomer
TestProcessOrder_NetworkFailure_Retries
TestProcessOrder_DuplicateRequest_Idempotent
```

You now know:
- The function does the obvious happy path thing.
- Empty input errors out.
- Out-of-stock triggers refund.
- Network failures retry.
- Duplicate requests are idempotent.

That's a lot of behavior absorbed without reading code.

### Read one test in detail

Pick a representative test — usually the happy path. Read it:

- **Setup** — what's needed to call the function?
- **Action** — how is the function called?
- **Assertion** — what's expected?

This shows you:
- The function's dependencies.
- The shape of valid input.
- The shape of expected output.

### Read the edge cases

Then read 2–3 edge-case tests. They show:

- What the author considered a "weird input."
- How the function handles errors (return value? exception? panic?).
- What invariants hold under unusual conditions.

The edge-case tests often have the most information per line.

## Test Organization Patterns

### Table-driven tests

```go
tests := []struct{
    name string
    input Order
    want Receipt
    wantErr bool
}{
    {"happy path", validOrder(), validReceipt(), false},
    {"empty", Order{}, nil, true},
    {"out of stock", outOfStockOrder(), nil, true},
}
```

Each row is a behavior spec. **Read the rows like a contract sheet.**

### BDD-style (Given/When/Then)

```python
def test_processes_order_when_inventory_available():
    # given
    order = make_order()
    inventory.stock(order.items)

    # when
    receipt = process_order(order)

    # then
    assert receipt.total == order.total
    assert inventory.remaining(order.items[0]) == 0
```

The structure itself is documentation.

### Property-based tests

```rust
proptest! {
    fn doesnt_panic_on_any_input(s in any::<String>()) {
        let _ = parse(&s);  // shouldn't panic
    }
}
```

These document **invariants** — properties that should hold for any
input. Powerful but harder to read; pair with example-based tests.

## What Tests Don't Tell You

Tests are great but not complete:

- **Untested behavior** — does the code do something the tests don't
  cover? Possibly buggy, possibly fine.
- **Performance characteristics** — most tests don't measure speed.
- **Concurrency behavior** — hard to test; often missing.
- **Real-world data** — tests use fixtures; production data is messier.
- **System-level behavior** — unit tests miss integration concerns.

Use tests as *one* source of truth; cross-reference with docs, types,
and code.

## When Tests Are Themselves Bad

You'll encounter:

- **Tautological tests**: `mock.foo.return_value = 42; assert mock.foo() == 42`.
  No information.
- **Tests asserting implementation, not behavior**: `assert internal_helper_called_twice`.
  Brittle, low-value.
- **Massive setup, tiny assertion**: 50 lines of arrangement for one
  `assert x == 1`.
- **Snapshot tests with no review**: `assertMatchesSnapshot(...)` —
  someone updated the snapshot without checking.

These are signals about code quality. They're also opportunities to
improve, if the project welcomes test refactoring.

## Test as Reproduction

When fixing a bug, write the failing test *first*:

```python
def test_user_can_login_with_uppercased_email():
    # currently fails — emails are case-sensitive
    user = create_user(email="Alice@example.com")
    assert can_login("alice@example.com", "pass") is True
```

Now you have a tight loop: run the test, see it fail, fix code, see it
pass. The test serves both as repro and as regression prevention.

See [../05-fixing-issues/test-first-fixing.md](../05-fixing-issues/test-first-fixing.md).

## Test as Specification

When designing a new feature, write the tests first, even if
informally:

```
test: user posts comment
  - returns 201
  - comment appears in /comments
  - mentioned users get notified
  - empty body returns 400
```

This forces you to specify behavior before implementing. Often catches
edge cases you'd otherwise miss.

## Reading Mocking Patterns

When tests use mocks, you learn:

- **What the function depends on** — anything mocked is a dependency.
- **The dependency's contract** — the mock encodes expected calls.
- **The author's testing strategy** — heavy mocks = unit isolation; few
  mocks = integration-style.

If mocks are extensive and confusing, it's often a sign the underlying
code has too many dependencies. Note it but don't refactor without
asking.

## Anti-Patterns

- **Skipping tests because "I'll read code instead."** Tests are
  faster.
- **Reading tests but not running them.** Run them to confirm the
  documented behavior actually holds.
- **Treating test code as second-class.** Sometimes test code is *more*
  carefully written than production code.

## See Also

- [reading-strategies.md](reading-strategies.md) — where tests fit in your reading flow
- [../05-fixing-issues/test-first-fixing.md](../05-fixing-issues/test-first-fixing.md)
- [../14-advanced/working-with-legacy-code.md](../14-advanced/working-with-legacy-code.md) — tests in legacy
