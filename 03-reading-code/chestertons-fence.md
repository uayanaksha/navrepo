# Chesterton's Fence

> "There exists in such a case a certain institution or law; let us say,
> for the sake of simplicity, a fence or gate erected across a road. The
> more modern type of reformer goes gaily up to it and says, 'I don't
> see the use of this; let us clear it away.' To which the more
> intelligent type of reformer will do well to answer: 'If you don't see
> the use of it, I certainly won't let you clear it away. Go away and
> think. Then, when you can come back and tell me that you do see the
> use of it, I may allow you to destroy it.'"
> — G.K. Chesterton

## Applied to Code

**Don't delete code, change behavior, or "simplify" something whose
purpose you don't understand.**

Weird, ugly, or seemingly-redundant code in a mature codebase is *usually*
load-bearing. The author was probably solving a problem you haven't
encountered yet.

## Why Code Looks Like A Fence

Top reasons code seems pointless:

1. **Bugfix for a non-obvious edge case.** "Why does it check for
   `null` here when the caller already validates?" → because three
   years ago, a different caller didn't.

2. **Performance optimization.** "Why is this loop unrolled?" →
   because the inner version was profiled as a hot path.

3. **Workaround for a third-party bug.** "Why this strange retry?" →
   because the upstream library has a known race condition.

4. **Compatibility shim.** "Why both code paths?" → because older
   clients can't handle the new behavior.

5. **Genuine cruft.** It happens, but less often than you think.

## The Investigation Protocol

When tempted to remove or change weird code:

### Step 1 — `git blame`

```bash
git blame -L <line>,<line> <file>
```

Get the SHA. Look at the message.

### Step 2 — `git show`

```bash
git show <sha>
```

Read the commit message. Look for issue/PR references.

### Step 3 — Read the referenced issue/PR

The PR usually explains the *why*. Issues often have user-reported bugs
the code is fixing.

### Step 4 — Search for related context

```bash
git log -S "FunctionName" --all  # other commits touching it
rg "FunctionName" --type-not test  # who calls this?
```

You may find a comment in a *different* file explaining the constraint.

### Step 5 — Decide

After investigation:

- **The fence is load-bearing.** Leave it alone, or extend it carefully.
- **The fence is obsolete.** Remove it explicitly: a PR with a clear
  rationale in the description ("Removed in <SHA> because the underlying
  bug was fixed upstream in <link>.").
- **The fence is unclear, but I have to change it.** Ask in the PR or
  in a project channel. Don't just push.

## Forms of the Fence

### Defensive null checks

```python
if user is not None and user.email is not None:
    send_welcome(user.email)
```

You think: "user can never be None here." Maybe. But the check might
exist because:
- A migration once produced incomplete user records.
- An internal API once returned None.
- The function is called from a test fixture that doesn't fully populate.

Investigate before "cleaning up."

### Magic numbers and timeouts

```go
ctx, cancel := context.WithTimeout(ctx, 27 * time.Second)
```

Why 27? Maybe arbitrary. But also maybe:
- The upstream service's load balancer kills at 30s.
- 27 leaves room for 3s of teardown.
- Past timeouts of 5s, 10s, 15s all caused incidents.

`git log -S "27 *" -p` may surface the story.

### Suppressed warnings / lint exceptions

```rust
#[allow(clippy::too_many_arguments)]
fn process_request(...) { ... }
```

The `allow` exists because someone considered the alternative worse.
Removing it forces a refactor that may be in scope or way out of scope.
Ask, don't assume.

### Try / except that "swallows" errors

```python
try:
    publish(event)
except Exception:
    pass
```

Could be lazy. Could also be:
- Best-effort fire-and-forget.
- A workaround for flaky downstream.
- Compensating for a specific known issue.

Investigate the original commit.

### Conditional imports / version checks

```python
if sys.version_info >= (3, 10):
    from importlib.metadata import packages_distributions
else:
    from importlib_metadata import packages_distributions
```

Sometimes you think "we dropped 3.9 support." Check the supported
versions before "simplifying."

### Test exclusions

```yaml
exclude:
  - test_legacy_login
```

Maybe legacy login is gone. Or maybe the test is flaky in CI but
important.

## When to Tear Down a Fence

After investigation, you can confidently remove or change a fence when:

1. The original reason is gone (the upstream bug is fixed, the legacy
   client is sunset, the migration is complete).
2. The PR description clearly explains the original purpose and why
   it's no longer needed.
3. You've added a test or comment so future-you doesn't reintroduce the
   problem.

## The Inverse: Adding Your Own Fences

When *you* write defensive or seemingly-redundant code, **leave a
comment explaining why**:

```python
# user.email can be None for accounts imported before 2024-03 — see issue #4521
if user.email:
    send_welcome(user.email)
```

This is one of the few cases where a code comment is justified — it's
not explaining *what*, it's explaining *why future-you should leave this
alone*.

## A Calibration Story

A team I worked with removed a `if x == 0: return` early-return because
it "looked weird." Three days later, customers reported a 10% error rate
on a specific endpoint. The early return was bypassing a code path that
had been broken for years but never hit because the early return shielded
it.

The fix wasn't to re-add the early return; it was to fix the broken code
path. But the team learned to investigate before deleting.

This is what Chesterton's fence is about. Not "never remove code." But
"understand before removing."

## When You Can't Investigate

Sometimes the original commit message is empty, the PR is private, or
the contributor left. You're stuck.

- **Default to keeping** the fence.
- **Ask in a project channel** if the project has one.
- **If you must change**, do it minimally and document your reasoning
  in your own commit message.
- **Add tests** before changing, so you catch regressions.

## See Also

- [../02-navigation/git-archaeology.md](../02-navigation/git-archaeology.md) — the investigation toolkit
- [../05-fixing-issues/root-cause-vs-symptom.md](../05-fixing-issues/root-cause-vs-symptom.md) — why the fence often *is* the fix
- [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md) — don't tear down fences while passing through
