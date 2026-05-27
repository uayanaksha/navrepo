# Feature Flags

A feature flag is a runtime / compile-time switch that gates a code
path. Used right, they de-risk releases. Used wrong, they accumulate
into a maintenance nightmare.

## When to Use

### Gradual rollout

Enable a feature for 1% of users, then 10%, then 100%. If problems
arise, flip back.

### A/B testing

Two implementations, measure which performs better.

### Staged migration

Old path and new path coexist; gradually shift traffic.

### Risk mitigation

The change is risky; flag lets you kill-switch.

### Coordinated releases

Frontend and backend change need to ship together; flag toggles both.

## When NOT to Use

### Permanent forks

A flag isn't a way to have two products forever. If you really need
two products, that's a different architecture.

### Avoiding a refactor

"Adding a flag to keep both code paths" can be a way to skip the
hard refactor. Sometimes legitimate; often laziness.

### Configuration

Per-deployment settings ("which Postgres host?") aren't feature flags
— they're config. Use config for config.

## Types of Flags

### Compile-time

```rust
#[cfg(feature = "new_oauth")]
pub fn login() { ... }
```

```go
// +build !legacy
```

Pros: zero runtime overhead, dead code eliminated.
Cons: requires rebuild to flip.

### Boot-time

```bash
APP_FEATURE_NEW_OAUTH=true ./app
```

Pros: change without rebuild.
Cons: requires restart.

### Runtime

```python
if feature_flag.enabled("new_oauth", user=current_user):
    return new_oauth(...)
return legacy_oauth(...)
```

Pros: per-user, per-org targeting; flip without restart.
Cons: complexity, performance overhead, debugging harder.

## Flag Implementation Spectrum

Simple → complex:

1. **Env var bool.** `if os.getenv("NEW_OAUTH") == "1": ...`
2. **Config file.** `flags: {new_oauth: true}` in YAML.
3. **Database-backed.** Flags table; reload periodically.
4. **External service.** LaunchDarkly, Unleash, Split, ConfigCat.

Use the simplest that meets your needs. A small project can do a lot
with env vars.

## Designing a Flag

### Name clearly

Bad: `feature_a`, `enable_new`, `temp_flag`.

Good: `new_oauth_flow`, `enable_billing_v2`, `rollout_inventory_caching`.

Future you (or your replacement) will read the flag name and need to
understand it.

### Document

Where does the flag live? What does it control? Who can flip it?

A central registry (a file, a wiki) of "active feature flags."

### Plan the removal

The most important property of a flag: **how does it go away?**

Without a plan, flags become permanent. Stale flags pile up. Code
becomes a maze of `if flag.x and not flag.y else flag.z`.

Set:
- A **target date** for removal.
- A **success metric** (e.g., "100% rollout for 2 weeks with no
  errors").
- A **person responsible.**

## The Flag Lifecycle

1. **Introduction.** Code paths gated; flag exists, off by default.
2. **Internal testing.** Flag on for dev / staging.
3. **Beta.** Flag on for select users.
4. **Gradual rollout.** Flag on for 1% → 10% → 50% → 100%.
5. **Stable.** Flag has been 100% for some time; no issues.
6. **Cleanup.** Remove flag and the old code path.

Without step 6, you have permanent dead branches.

## Cleanup

After the flag has been 100% for a while:

1. Remove the flag check sites in code.
2. Delete the now-unused old path.
3. Remove the flag from the flag system.
4. Update docs.

This is **non-optional**. A team that adds flags but never removes
them ends up with hundreds.

Schedule cleanup PRs explicitly. Make them small and reviewable.

## Anti-Patterns

### Permanent flag

A flag was meant to be temporary but stuck:

- Different customers depend on different defaults.
- Removing the flag would break someone.
- It's been around for years.

This is a code smell. Either:
- Bite the bullet and remove (with deprecation).
- Acknowledge it's permanent (rename, document, treat as config).

### Nested flags

```python
if flag.a:
    if flag.b:
        if flag.c:
            ...
```

Exponential code paths. Refactor.

### Flag-as-feature-name

Don't name flags after the feature; name them after the change:

Bad: `flag.oauth` (oauth is permanent; this flag is just one rollout).

Good: `flag.oauth_pkce_rollout` (specific to this rollout).

### Flag in tight loops

```rust
for item in items {
    if feature_flag.is_enabled("foo") { ... }
}
```

Check once outside the loop. Don't re-evaluate per iteration.

### Flag without metrics

You can't tell if the flag is doing what you intended. Add logging /
metrics so you know:
- How many users are seeing each branch.
- Error rates per branch.
- Performance per branch.

## Coordinated Cross-Service Flags

For changes spanning multiple services:

- Use the same flag name in all services.
- Use the same flag system (or sync them).
- Toggle in a coordinated fashion.

Or: introduce the new behavior on the server; have the client opt in
based on its own version.

## Flags and Tests

Test both branches:

```python
@pytest.mark.parametrize("flag_value", [True, False])
def test_login_works_with_flag(flag_value, mock_flag):
    mock_flag("new_oauth", flag_value)
    assert login("user", "pass").ok
```

If only one branch is tested, the other rots.

## Communicating Flags

When a flag affects users:

- Note in release notes ("OAuth PKCE available behind
  `new_oauth_flow` flag").
- Document in user-facing docs.
- Plan announcement for full rollout.

When a flag is internal-only:

- Don't expose it to users.
- Document internally.

## Permanent "Flags" That Aren't

Some "feature flags" are really:

- **Configuration**: per-deployment values.
- **Customer settings**: per-tenant options.
- **Subscription tiers**: paid vs free features.

These aren't temporary flags; they're permanent features of the
product. Treat them with their full lifecycle: docs, UI, billing.

## Tools

- **LaunchDarkly, ConfigCat, Split**: commercial.
- **Unleash, Flagsmith**: OSS.
- **Statsig, GrowthBook**: experimentation + flags.
- **Roll-your-own**: env vars + config.

Start simple. Adopt a service if you genuinely need its features
(targeting, instant flip, audit).

## See Also

- [migration-strategies.md](migration-strategies.md) — flags support migrations
- [deprecation.md](deprecation.md) — flags coexist with deprecation
- [scope-discipline.md](scope-discipline.md) — don't sneak features under flags
