# Fork Management

Keeping your fork in sync with upstream sounds easy until you skip it
for two months and find 200 conflicts.

## Initial Setup

```bash
# Clone your fork
git clone git@github.com:youruser/projectname
cd projectname

# Add upstream remote
git remote add upstream git@github.com:owner/projectname

# Verify
git remote -v
# origin    git@github.com:youruser/projectname (fetch)
# origin    git@github.com:youruser/projectname (push)
# upstream  git@github.com:owner/projectname (fetch)
# upstream  git@github.com:owner/projectname (push)
```

`origin` = your fork; `upstream` = the canonical repo.

You **fetch from upstream** and **push to origin**.

## Daily / Weekly Sync

Keep your fork's `main` current with upstream:

```bash
git fetch upstream
git checkout main
git merge --ff-only upstream/main
git push origin main
```

`--ff-only` ensures you only fast-forward (no merge commits). If it
fails, your local `main` diverged — fix that first.

### As a one-liner

```bash
git checkout main && git pull upstream main --ff-only && git push origin main
```

Or set up a shell alias:

```bash
alias gsync='git checkout main && git pull upstream main --ff-only && git push origin main'
```

### Using `gh`

GitHub CLI can sync your fork via the web API:

```bash
gh repo sync youruser/projectname
```

Same effect, less typing.

## When Sync Fails

### "Your branch has diverged"

Likely you committed to `main` directly. Don't do that. Recover:

```bash
git checkout main
git reset --hard upstream/main
git push origin main --force-with-lease
```

This nukes your local main. Make sure no work-in-progress lives there.

### Conflict during sync

Shouldn't happen if you never commit to `main`. If it does:

- Identify what's on your `main` that's not on upstream's.
- Move those commits to a branch.
- Reset `main` to upstream.

## Working on a Feature

```bash
# Always branch from a fresh upstream/main
git fetch upstream
git checkout -b feat/oauth-pkce upstream/main
# ... work ...
git push -u origin feat/oauth-pkce
```

Then PR from `youruser:feat/oauth-pkce` to `owner:main`.

## Updating a Feature Branch

After upstream has moved:

```bash
git fetch upstream
git rebase upstream/main
# resolve conflicts if any
git push --force-with-lease
```

Or merge:

```bash
git merge upstream/main
git push
```

See [branching.md](branching.md#updating-your-branch) for rebase vs
merge tradeoffs.

## Cleanup After PR Merges

```bash
# Pull the merged change into your local main
git checkout main
git pull upstream main

# Delete the merged branch
git branch -d feat/oauth-pkce
git push origin --delete feat/oauth-pkce

# Or: enable auto-delete on merge in your fork's settings
```

Stale branches in your fork are clutter. Delete liberally.

## Long-Term Fork Maintenance

If you maintain a fork *as a fork* (i.e., your own published variant
diverging from upstream):

- Sync upstream regularly.
- Document in README what's different from upstream.
- Periodically attempt to send your changes upstream.
- Tag releases distinctly (`youruser-v1.2`).

This is heavier than a contribution fork. See [../13-hidden-knowledge/notable-forks.md](../13-hidden-knowledge/notable-forks.md).

## Multiple Concurrent Branches

When you have 3+ PRs open:

```bash
# Sync main
git fetch upstream
git checkout main
git merge --ff-only upstream/main

# Update each feature branch
for b in feat/a fix/b feat/c; do
    git checkout $b
    git rebase main
    # resolve conflicts; cancel if too messy
done
```

Branches that don't rebase cleanly are warning signs — coordinate or
restructure.

## Cross-Account Forks

If you fork an organization's repo with multiple of your own GitHub
accounts, you can push between forks:

```bash
git remote add otheraccount git@github.com:otheraccount/projectname
git push otheraccount feat/oauth-pkce
```

Useful for: testing in CI from another account, sharing work, etc.

## Forks of Private Repos

GitHub forks of private repos are themselves private. Permissions
cascade.

Pushing to a private fork's branch and opening a PR to upstream
requires upstream maintainers' access.

For internal workflows: usually you branch directly in the canonical
repo, not fork it.

## Re-Forking

If your fork is hopelessly out of sync (years stale, conflicts
everywhere) and you have no in-flight work there:

1. Note any unmerged commits or branches.
2. Delete the fork.
3. Re-fork from current upstream.
4. Reapply any needed work as new branches.

Sometimes a clean slate beats hours of conflict resolution.

## Multi-User Forks

Some teams share a fork:

- One team account is the fork owner.
- All members push branches.
- PRs come from the team fork to upstream.

Manageable for small teams. Get complicated as teams grow.

## Fork as Personal Backup

Beyond contributions, your fork is also:

- A backup of your branches.
- A demo space for changes too rough for PRs.
- A staging ground for experiments.

Don't be shy about pushing many branches. Storage is cheap.

## See Also

- [branching.md](branching.md) — branch operations
- [../07-pull-requests/long-running-prs.md](../07-pull-requests/long-running-prs.md) — staying current during review
- [../11-tooling/git-config.md](../11-tooling/git-config.md)
