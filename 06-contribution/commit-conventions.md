# Commit Conventions

Commit messages aren't aesthetic. They are the **primary record of
why** code changed — for years after merge.

## What a Good Commit Looks Like

```
fix(auth): accept emails case-insensitively in login

Login was failing when users entered emails with uppercase characters
because `find_user_by_email` used a case-sensitive query. Normalize
email at the storage boundary instead of in each caller — covers
signup and password-reset too.

Fixes #4521
Signed-off-by: Your Name <your.email@example.com>
```

Three parts:
1. **Subject line**: short, imperative, ≤72 chars.
2. **Body**: motivation and detail. Wrap at ~72 chars.
3. **Footer**: issue refs, sign-offs, breaking-change notes.

## Subject Line

### Style guides

Most common conventions:

- **Imperative mood**: "fix bug" not "fixes bug" or "fixed bug." (Read
  as: "If applied, this commit will fix bug.")
- **Lowercase first word** (often, but not always).
- **No trailing period.**
- **≤50 chars hard, ≤72 soft cap.**

### Conventional Commits

A popular spec (`conventionalcommits.org`):

```
<type>(<scope>): <description>
```

Types:

- `feat`: new feature.
- `fix`: bug fix.
- `docs`: documentation only.
- `style`: formatting; no code change.
- `refactor`: code restructure; no behavior change.
- `perf`: performance improvement.
- `test`: adding or fixing tests.
- `chore`: routine maintenance.
- `build`: build system / external dep changes.
- `ci`: CI configuration.
- `revert`: reverting a previous commit.

Scope is the affected area: `feat(auth)`, `fix(orders)`, `docs(api)`.

Examples:

```
feat(auth): add OAuth PKCE support
fix(orders): prevent quantity zeroing on retry
docs(api): clarify pagination behavior
refactor(storage): extract Postgres pool config
chore: bump go to 1.22
```

### Other patterns

Not all projects use Conventional Commits. Other styles:

```
[component] add support for X
component: add support for X
component/subcomponent: add support for X
* component: add support for X
ENH: enhancement description
```

**Match the project's style.** Run `git log --oneline -30` to see what
recent commits look like.

## Body

### When to write a body

For trivial commits (typo, format), subject alone is fine.

For anything substantive, write a body. Cover:

- **Why** the change is being made.
- **What** the change does at a high level.
- **Tradeoffs** considered.
- **Risks** if any.

### What NOT to put in the body

- Mechanical detail you can see from the diff.
- "Cleanup" or "fixes" with no specifics.
- Reviewer-specific notes ("@alice please review the third commit").
  Those go in PR comments.

### Formatting

- Wrap at ~72 chars (some projects prefer 80).
- Use blank lines between paragraphs.
- Lists are fine if helpful:

```
fix(api): handle null user in profile lookup

Profile lookups were crashing when the user record had been
soft-deleted. Two changes:

- Return 404 instead of 500 for missing users.
- Update OpenAPI spec to document the 404.

Discovered via Sentry alerts after the 2025-03-15 deploy.
```

## Footer

### Issue references

```
Fixes #1234
Closes #1234
Refs #1234
```

GitHub auto-closes referenced issues when the PR merges to default
branch. Use `Fixes`/`Closes` for issues your PR fully resolves;
`Refs` for related-but-not-closed.

### Multiple issues

```
Fixes #1234
Fixes #1235
Refs #1200, #1201
```

### Breaking changes

Per Conventional Commits:

```
feat(api)!: rename `getUser` to `fetchUser`

BREAKING CHANGE: `getUser` has been removed. Use `fetchUser` instead.
```

The `!` after type/scope and the `BREAKING CHANGE:` footer combine to
signal a major-version bump in tools like `semantic-release`.

### Sign-off

For DCO projects:

```
Signed-off-by: Your Name <your.email@example.com>
```

Added automatically with `git commit -s`.

### Co-authoring

For pair-programming:

```
Co-authored-by: Pair Partner <pair@example.com>
```

GitHub displays both as authors.

## When to Squash

### Per-project policy

Check the project's merge button or CONTRIBUTING:

- **Squash merge**: your N commits become 1 on main.
- **Merge commit**: full history preserved.
- **Rebase merge**: linear history with all commits.

For **squash** projects: don't sweat intermediate commits. The maintainer
will write the final message.

For **rebase** projects: every commit should be clean and atomic. Squash
your "WIP" / "fix lint" / "fix CI" commits before requesting review.

### Cleanup with `git rebase -i`

```bash
git rebase -i main
# Edit each commit: pick / squash / fixup / reword / edit
```

`fixup` is like `squash` but discards the message. Useful for "fix
typo" follow-ups.

### Atomic commits

A good rebase-merged commit history looks like:

```
abc1234 feat(auth): add OAuth PKCE support
def5678 test(auth): cover PKCE happy path
ghi9012 docs(auth): update README with PKCE example
```

Each commit is self-contained and reviewable.

## Reverting

```bash
git revert <sha>
```

Creates a new commit that undoes <sha>. Message is auto-generated:

```
Revert "feat(auth): add OAuth PKCE support"

This reverts commit abc1234.
```

Edit if you want to explain why you reverted.

### Reverting a merge

```bash
git revert -m 1 <merge-sha>
```

`-m 1` says "treat first parent as the mainline; revert the merged
changes."

## Special Cases

### Bumping dependencies

```
chore(deps): bump react from 18.2 to 18.3

Patch release; no breaking changes.

Release notes: https://github.com/facebook/react/releases/tag/v18.3.0
```

Always link to release notes for significant bumps.

### Reverts of reverts

```
Reapply "feat(auth): add OAuth PKCE support"

This reapplies commit abc1234, originally reverted in def5678.
The original issue (CI flake) was fixed in ghi9012.
```

Include why it was reverted and why it's safe to reapply.

### Emergency fixes

When deploying urgently, commits can be terse, but follow up with proper
documentation:

```
fix(auth): emergency hotfix for login outage

Stop using cached user records that may be invalid post-migration.
Will follow up with proper test and root cause analysis in #4525.
```

A follow-up commit completes the record.

## Anti-Patterns

### Useless messages

```
fix
WIP
update file
changes
asdf
```

Never. Even in personal projects. Future you won't remember.

### Reviewer-as-author messages

```
addressed review comments
fix per @alice's suggestion
fix CI
```

These shouldn't make it to a clean history. Squash or rebase.

### Diff-restating messages

```
update line 42 of orders.go
```

The diff shows this. Tell us **why**.

### "Initial commit" for non-initial work

A 5000-line "initial commit" is hard to review. Break the import into
logical commits even at start.

## See Also

- [legal.md](legal.md) — sign-off and DCO mechanics
- [../07-pull-requests/](../07-pull-requests/) — your PR description complements the commit messages
- [branching.md](branching.md)
