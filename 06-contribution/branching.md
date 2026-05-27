# Branching

Branch hygiene is small in scope but compounding in benefit. A clean
branch is reviewable, mergeable, and forgettable.

## Branch Off the Right Base

### Identify the default branch

Most projects use `main`. Some use:

- `master` (older convention; some projects haven't migrated).
- `dev` / `develop` (when `main` is reserved for releases).
- `next` (for next-major work).
- `trunk` (rare; some Apache projects).

Check the repo's default branch on its host, or read CONTRIBUTING.

### Branch from latest

Always start from the latest:

```bash
git checkout main
git pull upstream main
git checkout -b feat/oauth-pkce
```

Branching from stale base means merge conflicts later.

### Branch from a specific commit

For "fix on top of someone's PR" or "off this release tag":

```bash
git checkout -b fix/follow-up-on-pr-1234 pr-1234-sha
# or
git checkout -b fix/cherry-pick v1.4.0
```

## Branch Naming

### Conventions

Common patterns:

```
feat/oauth-pkce
fix/login-uppercase-email
chore/bump-react-18
refactor/extract-auth-module
docs/api-pagination
test/coverage-orders
```

`<type>/<short-description>` is the most common shape.

Other patterns:
- `<username>/<description>` (`alice/oauth-pkce`).
- `<issue-id>-<description>` (`4521-uppercase-email`).
- `<type>-<description>` (no slash).

Match the project's style. Run `git branch -a` to see existing patterns.

### Keep names short

`feat/oauth` beats `feat/add-oauth-pkce-support-for-mobile-clients`.

Hyphens, not underscores or spaces.

### Don't include personal info

`yolo`, `wtf`, `final-fix-v3` etc. — keep it professional. Other people
read your branch names.

## One Branch Per Logical Change

If you're working on two unrelated things, use two branches.

```bash
# Working on feature A
git checkout -b feat/a

# Notice unrelated bug
git stash  # save in-progress feat/a work
git checkout main
git checkout -b fix/unrelated
# fix it, commit, push

# Resume feat/a
git checkout feat/a
git stash pop
```

The temptation to "while I'm here, also fix..." is constant. Resist.
Separate branches keep PRs focused.

## Working from a Fork

The standard OSS pattern:

```bash
# fork on GitHub, then:
git clone git@github.com:youruser/projectname
cd projectname
git remote add upstream git@github.com:owner/projectname
git fetch upstream
```

You now have:
- `origin` = your fork.
- `upstream` = the canonical repo.

### Creating a branch

```bash
git checkout -b feat/oauth-pkce upstream/main
```

This branches from upstream's latest, not your possibly-stale fork.

### Pushing

```bash
git push -u origin feat/oauth-pkce
```

`-u` (`--set-upstream`) makes future `git push` shorter.

### Keeping your fork's main current

```bash
git checkout main
git pull upstream main
git push origin main  # update your fork
```

Some projects suggest you never commit to your fork's `main`. Treat it
as a mirror of upstream.

## Updating Your Branch

If `main` has moved while you've been working:

### Rebase

```bash
git fetch upstream
git rebase upstream/main
```

Replays your commits on top of `main`. Clean linear history.

Resolve conflicts as they appear. After:

```bash
git push --force-with-lease
```

Note: `--force-with-lease` is safer than `--force` — it refuses if
your remote has changes you haven't seen.

### Merge

```bash
git fetch upstream
git merge upstream/main
```

Creates a merge commit. Preserves your commit history but adds a merge
commit to it.

### Which to use

Per project convention. Common:

- **Squash-merge projects**: either works. Rebase keeps your local
  history clean; merge is safer if you're paranoid about losing
  commits.
- **Rebase-merge projects**: use rebase.
- **Merge-commit projects**: use merge.

When in doubt, rebase keeps things tidy. Just know that force-pushing
during review can lose reviewer inline comments — see [../07-pull-requests/handling-ci.md](../07-pull-requests/handling-ci.md).

## Long-Running Branches

Branches that survive weeks need maintenance:

- **Rebase / merge main weekly.** Stale branches accumulate conflicts.
- **Don't let your branch fork far** from main.
- **Consider splitting** if the branch is sprawling — large branches
  rarely merge.

See [../07-pull-requests/long-running-prs.md](../07-pull-requests/long-running-prs.md).

## Cleanup

### Delete merged branches

After merge:

```bash
# locally
git checkout main
git branch -d feat/oauth-pkce

# on your fork
git push origin --delete feat/oauth-pkce
```

GitHub can auto-delete on PR merge if you enable it in your fork
settings. Recommended.

### Delete all merged branches

```bash
git branch --merged main | grep -v '^[* ] main$' | xargs git branch -d
```

Once a branch merges, delete it. Otherwise old branches accumulate.

### Recovering a deleted branch

If you regret deletion:

```bash
git reflog
# find the SHA at branch tip
git branch <new-name> <sha>
```

Reflog is your safety net.

## Working with Multiple Branches Simultaneously

`git worktree` lets you have multiple branches checked out in different
directories:

```bash
git worktree add ../projectname-feat-a feat/a
git worktree add ../projectname-fix-b fix/b
```

Now `../projectname-feat-a` and `../projectname-fix-b` are independent
working directories on different branches. Useful when you switch
between branches often.

```bash
git worktree list      # see all worktrees
git worktree remove <path>  # clean up
```

## Branch Protection on Main

Most projects protect `main`:

- Can't push directly.
- Required reviews before merge.
- Required CI before merge.

This is normal. Always PR.

Force-pushing to main, even when allowed, is dangerous. Don't.

## Branching for Releases

Some projects branch per release:

- `release/v1.2` — frozen for that minor version.
- Patches go to `release/v1.2` and cherry-picked to `main`.
- Or `main` is on next; `release/v1.2` is the current stable.

Read the project's release docs. Don't just PR to main if there's a
release branch.

## See Also

- [fork-management.md](fork-management.md)
- [../07-pull-requests/](../07-pull-requests/) — once your branch is ready
- [../11-tooling/git-config.md](../11-tooling/git-config.md)
