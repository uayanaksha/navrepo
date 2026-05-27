# Backwards Compatibility

Fixing a bug shouldn't break callers. Sometimes a "fix" is actually a
breaking change in disguise.

## The Question to Ask

For every bug fix, ask: **could existing callers depend on the buggy
behavior?**

If yes, your "fix" is a breaking change.

## Examples

### Implicit dependency on the bug

Code:

```python
def get_user(email):
    return User.objects.filter(email=email).first()
```

Bug: returns None instead of raising NotFoundError when no user exists.

You "fix":

```python
def get_user(email):
    return User.objects.get(email=email)  # raises if not found
```

Now every caller doing `user = get_user(email); if user: ...` breaks.

### Adding "fixes" that change shape

Original:

```typescript
function getOrder(id: string): Order
```

You add nullability for "missing" cases:

```typescript
function getOrder(id: string): Order | null
```

Every caller that didn't handle null now has a type error (good!) — or
worse, in untyped code, a runtime crash.

### Field semantics changes

Before: `created_at` is in user-local time.
After: `created_at` is in UTC.

The field name didn't change. The format didn't change. But every
client that displays this is now wrong by a few hours.

## When Compatibility Matters

It depends on your audience:

| Audience | Compatibility expectation |
|---|---|
| Single internal team | Coordinate; can break with a heads-up |
| Many internal teams | Need migration plan |
| External users (OSS library) | Strong expectation; semver respected |
| Public API consumers | Very strong; deprecation periods required |
| Third-party plugins | Strong; document changes |

For a hobby project, you can do anything. For a library with users,
you can't.

## Semantic Versioning, Briefly

Most OSS projects follow SemVer (`MAJOR.MINOR.PATCH`):

- **PATCH** (1.2.3 → 1.2.4): bug fixes, no API change.
- **MINOR** (1.2 → 1.3): additions, backwards compatible.
- **MAJOR** (1 → 2): breaking changes.

A "fix" that breaks compatibility should be a major version bump. If
the project is pre-1.0, looser rules apply.

## Strategies for Compatible Fixes

### 1. Fix at a lower layer

If the bug is in user-facing code, fix at a deeper layer where no one
depends on the buggy behavior.

### 2. Add a new function, deprecate the old

```python
@deprecated("Use get_user_or_none instead")
def get_user(email):
    # buggy behavior preserved for compat
    return User.objects.filter(email=email).first()

def get_user_or_none(email):
    # new, correct behavior
    return User.objects.filter(email=email).first()

def get_user_or_raise(email):
    # also new
    return User.objects.get(email=email)
```

Old callers keep working. New callers use the new APIs. Eventually
the deprecated one is removed.

### 3. Flag the new behavior

```python
def get_user(email, *, strict=False):
    if strict:
        return User.objects.get(email=email)
    return User.objects.filter(email=email).first()
```

Or via env var, config, feature flag. Default to old behavior; allow
opt-in.

### 4. Version the API

For HTTP APIs:

```
GET /v1/users   → old buggy behavior
GET /v2/users   → fixed behavior
```

Heavyweight; usually for major APIs.

## When Breaking Is Necessary

Sometimes the bug is security-critical or the cost of preserving
compatibility is too high.

Mitigation:

1. **Document loudly.** CHANGELOG, release notes, blog post if
   warranted.
2. **Bump major version.**
3. **Provide migration tools.** A codemod, a script, a clear
   step-by-step.
4. **Long deprecation period** if possible.

For OSS: give downstream users at least one release of warning.

## Deprecation Mechanics

Adding deprecation:

```python
import warnings

def old_func():
    warnings.warn(
        "old_func is deprecated; use new_func instead",
        DeprecationWarning,
        stacklevel=2,
    )
    return new_func()
```

```rust
#[deprecated(since = "0.5.0", note = "use new_func")]
pub fn old_func() { ... }
```

```typescript
/** @deprecated use newFunc instead */
function oldFunc() { ... }
```

Then in your changelog:
- Note the deprecation.
- Note the version where it'll be removed.
- Provide migration guidance.

## Removing Deprecated Code

After the announced removal version:

- Remove the deprecated symbol.
- Note in CHANGELOG under "Breaking changes."
- Bump major version.

Don't sneak removals into minor versions. Users depend on the
deprecation period.

## Compatibility in Internal Codebases

Even internal teams care:

- **Service contracts.** Other services consume yours; coordinate.
- **Database schemas.** Migrations can break running code.
- **Build artifacts.** Other teams may depend on the file structure.

For internal: announce upcoming changes, coordinate timing, provide
migration help.

## Database Migrations

A special class of compatibility:

### Backward-compatible migrations

These can be deployed before code changes:

- Adding a column (nullable).
- Adding an index.
- Adding a new table.

### Code-coupled migrations

These require coordinated deploys:

- Removing a column (code must stop using it first).
- Renaming a column (use expand-contract: add new, dual-write, switch
  reads, remove old).
- Changing a constraint.

Use migration tools that support phased rollouts.

## The "Sunset" Pattern

For larger compatibility breaks:

1. **Announce** intent. Issue, blog post, mailing list.
2. **Provide alternative** in current version.
3. **Mark deprecated**, with migration link.
4. **Wait** at least one release.
5. **Add warnings** that become more visible.
6. **Remove** in major version.

Each step gives users time to react.

## What Counts as "Existing Callers"

For internal code: known consumers within your codebase.

For libraries / OSS: assume the worst.

- Someone is calling your function in a way you didn't anticipate.
- Someone is depending on edge-case behavior.
- Someone is parsing your error messages.

The wider your audience, the more "weird" usage exists.

## Compatibility Testing

For libraries with major users:

- Test against the previous version's tests, not just current.
- Add an `Compatibility` test suite that pins behaviors that **must
  not change**.
- For API endpoints: contract tests.

## See Also

- [fix-surface-area.md](fix-surface-area.md) — small fixes are usually compatible
- [../10-features-refactors/deprecation.md](../10-features-refactors/deprecation.md) — deeper on deprecation
- [../13-hidden-knowledge/maintainer-calculus.md](../13-hidden-knowledge/maintainer-calculus.md) — why maintainers care about compat
