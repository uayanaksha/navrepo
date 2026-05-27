# PR Description

A reviewer should not have to read your diff to know what your PR does.
The description does that. The diff is supporting evidence.

## The Default Template

```markdown
## Summary
One sentence: what this PR changes and why.

## Motivation
The user-visible problem or use case. Link the issue.

## Changes
- Bullet list of the substantive changes.
- One bullet per logical unit.

## Test plan
- [ ] Unit tests added for X
- [ ] Manually verified Y in <environment>
- [ ] Existing tests pass

## Risks / Tradeoffs
What this might break. What we considered and rejected.

## Screenshots / Videos
(For UI changes — non-negotiable.)
```

Use this even when the project doesn't have a template. Maintainers
notice.

## Section-by-Section

### Summary

One sentence. Maintainers read this in the PR list. Make it count.

Bad: "Updated some files."
Good: "Fixes login failure when email contains uppercase characters."

### Motivation

Why does this PR exist? Link the issue if one exists.

```markdown
## Motivation

Users with email addresses containing uppercase characters (e.g.,
`Alice@example.com`) can't log in — they receive a 500. This affects
~2% of users who originally signed up before our email normalization
was added. See #4521.
```

Reviewers need to know: is this work needed? Was it agreed-to? How urgent?

### Changes

Reviewer-oriented summary of *what* you did:

```markdown
## Changes

- Normalize email at `find_user_by_email()` (storage boundary).
- Add test for case-insensitive lookup.
- Update OpenAPI spec to document the new behavior.
```

Each bullet should map to a coherent change in the diff. Reviewers
use this to navigate the diff.

### Test plan

Checkboxes are a powerful signal — they show you actually verified.

```markdown
## Test plan

- [x] Added `test_login_accepts_uppercased_email` (passes locally)
- [x] Ran full test suite locally (`make test`)
- [x] Manually verified login with `Alice@example.com` in dev
- [x] Verified normal-case logins still work
```

Tick what you actually did. Don't fake-tick.

If you couldn't test something, say so:

```markdown
- [ ] Couldn't test against production DB; needs staging deploy first
```

### Risks / Tradeoffs

Be honest about uncertainty:

```markdown
## Risks

- Existing emails stored with mixed case will now match incoming
  lowercase lookups. No data migration needed; this is symmetric.
- If anyone relies on case-sensitive matches (e.g., a legacy admin
  endpoint), they'd be affected. Audited and found none.

## Tradeoffs

Considered normalizing at signup but that doesn't help existing users.
Considered DB-level COLLATE — would work but harder to roll back if
needed. Boundary normalization is the smallest reversible change.
```

This signals deep thinking. Maintainers trust thinkers.

### Screenshots / Videos

For UI changes: **non-negotiable**.

For CLI changes: paste the actual output before and after.

For API changes: paste the actual response before and after.

```markdown
## Screenshots

Before:
![error 500 on uppercase login](https://...)

After:
![successful login with Alice@example.com](https://...)
```

### Links

```markdown
## Related

- Fixes #4521
- See also #4520 for related bug
- Depends on #4522 (must merge first)
- Migration follow-up tracked in #4525
```

## When To Tailor

### For trivial PRs

A typo fix doesn't need a full template. A title + one-sentence
description is fine.

But: never skip motivation if the change is non-obvious.

### For doc-only PRs

Skip "Test plan" if there's nothing to test beyond rendering. Add a
preview link if your docs build that way.

### For dep upgrades

```markdown
## Summary
Bump react from 18.2 to 18.3.

## Motivation
Patch release; security fix in [CVE-2024-XXXX](link).

## Risks
None expected. Reviewed release notes — no breaking changes.

## Test plan
- [x] `npm install` succeeds
- [x] `npm test` passes
- [x] Manually opened app in browser, no regressions visible
```

### For refactors

Emphasize "no behavior change":

```markdown
## Summary
Extract `OrderService` from inline handler logic. No behavior change.

## Motivation
Preparing for #4530 (add new order type) by isolating order logic.

## Changes
- New file `services/orders.go` with extracted methods.
- Handler now delegates to service.
- Existing tests pass unchanged.

## Verification
- [x] Existing test suite passes (no behavior change)
- [x] Diff is a pure restructure (verified by manual review)
```

### For feature PRs

Add a "Demo" section:

```markdown
## Demo
[video showing the new feature in action]

## What this enables
Once merged, users can do X by Y. See updated docs in `docs/features/X.md`.
```

## Things to Include That People Miss

### A reviewer guide for large PRs

For PRs > 500 lines:

```markdown
## Reviewer guide

Most substantive changes:
- `service/orders.go` (new file, ~120 lines)
- `handlers/orders.go:31-58` (handler refactor)

Mechanical / boilerplate:
- `proto/order.pb.go` (regenerated; expect changes)
- `test/fixtures/order.json` (new test data)

Suggested review order:
1. Read `service/orders.go` to understand the new abstraction.
2. Review handler delta to see how it uses the service.
3. Skim tests to see expected behavior.
4. Generated code can be skipped.
```

This single section can halve review time on large PRs.

### Out-of-scope explicitly

```markdown
## Out of scope (intentional)

- Refactoring `LegacyOrderHandler` — tracked in #4530
- Migrating existing data to new format — separate PR
- Performance optimization of bulk inserts — future work
```

Heads off the most common review pushback ("could you also fix...?").

### Backwards compatibility statement

```markdown
## Backwards compatibility

No external API changes. Internal function `find_user()` previously
returned None for missing users; still does. Behavior identical for
all currently-supported call sites.
```

## What Not to Include

- **Implementation details a reader can see from the diff.** ("In line
  42 I changed the variable name.")
- **Reviewer-specific notes.** ("@alice please look at this part.") Use
  inline review comments.
- **Bragging.** ("This is way cleaner than what was there before.")
- **Defensive justifications.** ("I know this is weird but trust me.")
  Just explain the choice.

## When You Don't Know How to Describe It

Sometimes you start a PR and the description is hard to write. That
itself is a signal:

- The PR might be doing too many things (split).
- You might not understand your own change well enough.
- The motivation might be unclear (revisit "why am I doing this?").

A PR whose description writes itself easily is a PR that's ready to
merge.

## Edit the Description as You Go

The description isn't final on first commit. Update it as:

- Scope changes.
- New tests are added.
- Reviewer raises a concern you addressed.

A descriptive PR header at merge time is gold for archeology later.

## See Also

- [pr-size.md](pr-size.md) — keep small enough that the description is short
- [self-review.md](self-review.md) — write description, then review your own diff
- [visual-artifacts.md](visual-artifacts.md) — what to include for visible changes
