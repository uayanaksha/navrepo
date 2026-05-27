# Deprecation

Removing public API is rude. **Deprecation paths** soften the blow —
give users time to migrate before removal.

## The Standard Pattern

Four phases, spread over multiple releases:

1. **Add** the new API alongside the old.
2. **Mark** the old as deprecated.
3. **Wait** at least one release.
4. **Remove** the old.

Each phase is its own change. Some take days; some take months.

## Phase 1: Add

The new API exists. Old still works. No warnings.

```rust
// old:
pub fn process(order: Order) -> Result<Receipt, Error>

// new (alongside):
pub fn process_with_options(order: Order, opts: Options) -> Result<Receipt, Error>
```

Document both. The new one is preferred; the old one continues.

## Phase 2: Mark Deprecated

The old API still works but signals it's going away:

### Compile-time warnings

```rust
#[deprecated(since = "0.5.0", note = "use process_with_options")]
pub fn process(order: Order) -> Result<Receipt, Error> { ... }
```

```python
import warnings

def process(order):
    warnings.warn(
        "process() is deprecated; use process_with_options()",
        DeprecationWarning,
        stacklevel=2,
    )
    ...
```

```typescript
/** @deprecated use processWithOptions() */
export function process(order: Order): Receipt { ... }
```

```java
@Deprecated(since = "0.5.0", forRemoval = true)
public Receipt process(Order order) { ... }
```

```go
// Deprecated: use ProcessWithOptions instead.
func Process(order Order) (Receipt, error) { ... }
```

### Documentation updates

- CHANGELOG entry: "Deprecated `process()`; use `process_with_options()`."
- API docs: marked with deprecation note.
- README example updated to the new API.

### Migration guide

For non-trivial deprecations, add a migration doc:

```markdown
# Migrating from process() to process_with_options()

## Why

`process_with_options` allows passing configuration that `process`
couldn't.

## Before

result = process(order)

## After

result = process_with_options(order, Options.default())
```

The clearer the path, the less pain for users.

## Phase 3: Wait

At least one release. Often more.

Why wait:
- Users need time to upgrade.
- Some are pinned to a version and won't see the deprecation until
  they upgrade.
- Library users have their own release cycles.

For widely-used libraries: 6 months to 2 years of deprecation.

For internal code: shorter, maybe weeks.

## Phase 4: Remove

Final removal:

```rust
// process() is now gone
pub fn process_with_options(order: Order, opts: Options) -> Result<Receipt, Error> { ... }
```

- Removed in a **major version bump** (SemVer 2.0 → 3.0).
- Noted prominently in CHANGELOG under "Breaking changes."
- Migration guide kept available even after removal.

## When to Skip the Deprecation Path

Rare cases:

- **Security vulnerability** — broken API must be removed urgently.
- **Pre-1.0 / early library** — versioning rules are looser.
- **Internal-only API** — coordinate with all callers; remove cleanly.

In these cases, **communicate loudly** even without formal deprecation.

## Different Audiences, Different Speed

| Audience | Deprecation period |
|---|---|
| Solo project | None / few days |
| Internal team | Weeks |
| Internal multi-team | A release or two |
| OSS library, small | Several months |
| OSS library, popular | A year or more |
| Public API | 6 months minimum, often years |
| Enterprise commercial | Multi-year |

Larger audience = longer period. The cost of breaking N users scales
with N.

## Soft vs Hard Removal

### Soft

Mark deprecated; don't remove. May stay deprecated for years.

```python
@deprecated("rarely-used; consider migrating but no rush")
def legacy_function(): ...
```

Adds compile-time noise but preserves compatibility.

### Hard

Mark deprecated; remove in next major.

```python
@deprecated("REMOVAL planned in v2.0; please migrate")
def legacy_function(): ...
```

Soft is friendlier but accumulates cruft. Hard is cleaner but
demanding.

Match scope:
- Soft for low-value cruft that's rarely used.
- Hard for code with maintenance burden.

## Deprecating with Replacements

Always (when possible) replace before deprecate:

✅ "Use `new_func()` instead."

❌ "Deprecated. No replacement; figure it out."

The latter is hostile. If you really must remove without replacement,
explain why.

## Deprecating Behavior (Not Just API)

Sometimes the API stays but behavior changes:

```python
def parse(s, strict=False):
    if not strict:
        warnings.warn(
            "default non-strict parsing will be removed in 2.0; "
            "use strict=True or explicit strict=False"
        )
        ...
```

The function signature stays. The default changes. Warn users that
relying on the default is fragile.

## Test Deprecated Code

Until removal, deprecated code is **still supported**. Test it.

- Keep tests for deprecated APIs.
- Mark them clearly: `def test_deprecated_process(): ...`
- Remove when API is removed.

Don't let deprecated code rot — that's its own bug source.

## Deprecation in OSS

When deprecating an OSS API:

- **Blog post / announcement** for major deprecations.
- **Discussion / RFC** ahead of deprecation if controversial.
- **Migration tools** if feasible (a codemod, a script).
- **Long deprecation period** — your users have their own schedules.

For projects you depend on: subscribe to release notes. Don't be
surprised by removals.

## Deprecation Mistakes

### Deprecating without alternative

> "Deprecated. Don't use."

What should I use instead? Always answer this.

### Removing without deprecation

A "fix" PR that removes a public function. Users are blindsided.

### Deprecation that drags forever

Functions marked deprecated 5 years ago, still around. The deprecation
period is long enough; remove.

### Re-adding removed

Removed in v2.0; user complaints; re-added in v2.1. Confusing.
Stand by the decision or don't make it.

### Inconsistent deprecation policy

Some APIs deprecated for a year; others removed without notice. Be
consistent so users know what to expect.

## See Also

- [../05-fixing-issues/backwards-compatibility.md](../05-fixing-issues/backwards-compatibility.md)
- [migration-strategies.md](migration-strategies.md)
- [feature-flags.md](feature-flags.md) — for gradual *introduction* of replacements
