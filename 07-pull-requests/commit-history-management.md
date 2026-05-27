# Commit History Management

Three merge strategies exist. Each has tradeoffs. Match the project's
convention and you'll be fine.

## The Three Strategies

### Squash and merge

Your N commits collapse into 1 commit on main.

```
Before merge:
main: A → B → C
feat:  ↓ → D → E → F

After squash-merge:
main: A → B → C → DEF (single commit "feat: D + E + F")
```

**Pros:**
- Linear, simple history on main.
- Individual feature commits' messiness doesn't matter (WIP, fix-lint,
  etc.).
- One commit per PR — easy to revert.

**Cons:**
- Lose granular history.
- Multiple logical changes in one commit.
- `git bisect` resolution is per-PR, not per-commit.

### Merge commit

Your branch is merged with a merge commit, preserving full history.

```
Before merge:
main: A → B → C
feat:  ↓ → D → E → F

After merge:
main: A → B → C → M (merge commit)
              ↘ D → E → F ↗
```

**Pros:**
- Full per-commit history.
- Clear "this PR brought these changes."
- Easy to revert the merge.

**Cons:**
- History gets bushy.
- Intermediate commits may be broken (don't build, fail tests).
- "WIP" commits visible forever.

### Rebase and merge

Each commit from your branch is replayed onto main.

```
Before merge:
main: A → B → C
feat:  ↓ → D → E → F

After rebase-merge:
main: A → B → C → D' → E' → F'
```

**Pros:**
- Linear history.
- Per-commit granularity preserved.
- Each commit independently buildable (in principle).

**Cons:**
- Requires clean commits — every commit on the branch must be ready.
- Force-pushes happen frequently before merge.
- "Same PR" not visible without searching.

## What to Use When

If the project decided, **follow theirs**. Don't fight the convention.

If you're choosing for a new project:

- **Solo / small projects**: rebase-merge or squash. Either works.
- **Medium projects, fast-moving**: squash. Less ceremony.
- **Large projects, careful history**: rebase-merge. Bisect-friendly.
- **Anything with merge commits as convention**: merge-commit. Don't
  overthink.

## How to Tell the Project's Convention

### Look at recent merges

```bash
git log --oneline -20 main
```

If you see:
- Many merge commits (`Merge pull request #N`): merge-commit.
- Sequence of unrelated single commits: squash.
- Sequence of related commits from PRs: rebase-merge.

### Look at the merge button

GitHub: open any open PR. The "Merge" button shows the configured
default ("Squash and merge," "Create a merge commit," "Rebase and
merge"). Only one is enabled, usually.

### Check CONTRIBUTING.md

Often says explicitly.

## Cleaning Up Your Branch

For projects with **squash-merge**: don't sweat your intermediate
commits. The maintainer (or auto-merge) writes the final message.

For projects with **merge-commit**: clean up your branch so the merged
history is good:

```bash
git rebase -i main
# squash WIP commits, drop noise, reword cleanly
```

For projects with **rebase-merge**: every commit should be clean.

## Atomic Commits

For projects that preserve commit history (merge-commit, rebase-merge):

A good commit is **atomic**: a self-contained, buildable, reviewable
change.

Bad:
```
abc1234 WIP
def5678 more work
ghi9012 fix typo
jkl3456 actual feature
mno7890 fix tests
```

Good:
```
abc1234 refactor: extract OrderValidator
def5678 feat: validate order quantity in OrderValidator
ghi9012 test: cover OrderValidator edge cases
```

Each commit could merge on its own.

## Squash-Merge Final Commit

When merging via GitHub squash:

- The PR title becomes the commit message subject.
- The PR description (or list of commit subjects) becomes the body.

So: write a clean PR title. It outlives your branch.

Example PR title: `fix(auth): accept emails case-insensitively`
Example commit on main: `fix(auth): accept emails case-insensitively (#1234)`

GitHub appends the PR number automatically.

## Force-Pushing Your Branch

When you rebase or amend, your branch diverges from the remote. Push:

```bash
git push --force-with-lease
```

`--force-with-lease` is safer than `--force`:
- `--force`: overwrites remote even if it has commits you don't have.
- `--force-with-lease`: refuses if remote has unexpected changes.

Always `--force-with-lease`. Never `--force` on shared branches.

## Force-Pushing During Review

Force-pushing breaks reviewer state:

- Inline comments on lines that no longer exist are lost / orphaned.
- Reviewers must re-read.

To avoid:

- During active review, push regular commits (no force).
- Squash / rebase before requesting review or at end.
- If you must force-push, **say so** in a top-level comment:

> "Force-pushed after rebasing main. Substantive changes only in
> `service/orders.go` per Alice's feedback."

## Reviewer-Driven Force-Push

If a reviewer suggests "rebase on main" or "squash these," do it
when convenient. Sometimes once at end is better than after each
review.

```bash
git rebase main
# resolve conflicts if any
git push --force-with-lease
```

Or:

```bash
git rebase -i main
# squash commits
git push --force-with-lease
```

## Stacked Force-Pushes

In stacked PRs, force-pushing the base requires force-pushing
descendants:

```bash
git checkout base-branch
git rebase main
git push --force-with-lease

git checkout next-branch
git rebase base-branch
git push --force-with-lease

# etc.
```

Stack tools automate this.

## Recovering from Bad Rebase

`git reflog` always has the previous state:

```bash
git reflog
# find pre-rebase SHA
git reset --hard <sha>
git push --force-with-lease
```

90 days of safety net.

## Commit Author / Committer

Note: `git commit --amend` and rebase change the committer (you) but
preserve the author (original author).

For multi-author work:

```bash
git commit -m "feat: ..." --author="Coauthor Name <coauthor@example.com>"
```

Or add `Co-authored-by:` in the body:

```
feat: add new feature

Co-authored-by: Pair Partner <pair@example.com>
```

GitHub shows both.

## Anti-Patterns

### Committing WIP and never cleaning up

OK during development. Not OK at merge time on history-preserving
projects.

### Force-pushing without telling reviewers

Surprise. Confusion. Slowed review.

### `git push -f` (without `--with-lease`)

You'll eventually overwrite someone's work. `--force-with-lease`
prevents this.

### Rewriting history on `main`

Never. Even if `main` is yours.

## See Also

- [../06-contribution/commit-conventions.md](../06-contribution/commit-conventions.md) — what good commits look like
- [../06-contribution/branching.md](../06-contribution/branching.md) — branch lifecycle
- [stacked-prs.md](stacked-prs.md)
