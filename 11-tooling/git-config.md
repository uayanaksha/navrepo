# Git Config

Git out of the box is fine. Git tuned to your workflow is a different
tool. A few config lines remove the most common daily friction.

## The Global Config Worth Setting

Put these in `~/.gitconfig` (or run `git config --global`). Each one
removes a recurring annoyance.

```ini
[init]
    defaultBranch = main

[pull]
    rebase = true            # no merge commits on `git pull`

[push]
    autoSetupRemote = true   # `git push` on a new branch just works
    followTags = true        # push annotated tags with commits

[fetch]
    prune = true             # delete local refs for deleted remote branches

[rebase]
    autosquash = true        # fixup!/squash! commits reorder automatically
    autostash = true         # stash/unstash around a rebase

[merge]
    conflictstyle = zdiff3    # show the common ancestor in conflicts

[diff]
    algorithm = histogram     # better diffs than the default (myers)
    colorMoved = zebra        # distinguish moved lines from added/removed
    mnemonicPrefix = true     # i/ w/ c/ instead of a/ b/

[rerere]
    enabled = true            # remember conflict resolutions (see below)

[branch]
    sort = -committerdate     # most recent branches first in `git branch`

[column]
    ui = auto                 # multi-column branch/status output
```

### Why each matters

| Setting | What it fixes |
|---|---|
| `pull.rebase = true` | No accidental merge bubbles from `git pull` |
| `push.autoSetupRemote` | No more `--set-upstream origin branch` dance |
| `fetch.prune` | Stale `origin/old-branch` refs don't pile up |
| `rebase.autosquash` | `git commit --fixup` cleanups land in the right spot |
| `rebase.autostash` | Rebase with a dirty tree, no manual stash |
| `merge.conflictstyle = zdiff3` | Conflicts show the original, not just both sides |
| `diff.algorithm = histogram` | Cleaner, more human diffs on refactors |
| `rerere.enabled` | Resolve a recurring conflict once, not ten times |

## rerere — Reuse Recorded Resolution

The single most underused git feature. When you resolve a conflict,
`rerere` records the resolution. The next time the *same* conflict
appears, git resolves it for you automatically.

This is gold for:

- Long-running branches you rebase repeatedly.
- Conflicts that reappear every time you `rebase` onto a moving `main`.
- Maintaining a patch series.

```bash
git config --global rerere.enabled true
```

Then, just resolve conflicts normally. Git prints:

```
Recorded resolution for 'src/handler.py'.
```

Next time, it prints `Resolved 'src/handler.py' using previous
resolution.` and you only review.

To see what's recorded: `git rerere status`, `git rerere diff`.

## Aliases That Earn Their Keep

```ini
[alias]
    s = status -sb
    co = checkout
    br = branch
    cm = commit -m
    amend = commit --amend --no-edit
    unstage = restore --staged

    # one-line graph log
    lg = log --oneline --graph --decorate --all -30

    # what changed vs the base branch
    diffbase = "!git diff $(git merge-base HEAD main)"

    # files changed in this branch vs main
    files = "!git diff --name-only $(git merge-base HEAD main)"

    # last commit's contents
    last = show --stat HEAD

    # interactive rebase onto N commits back
    reb = "!f() { git rebase -i HEAD~${1:-10}; }; f"

    # undo last commit, keep changes staged
    uncommit = reset --soft HEAD~1

    # find a commit touching a string (pickaxe)
    pick = log -p -S

    # who-and-when for a file's lines
    praise = blame

    # prune merged local branches
    sweep = "!git branch --merged main | grep -v -E 'main|master|\\*' | xargs -r git branch -d"
```

`git lg`, `git s`, and `git diffbase` alone change your day.

Shell aliases (in your rc) are different from git aliases (in
gitconfig). Git aliases work everywhere git runs — CI, other shells,
other machines that share your dotfiles. Prefer them for git-specific
shortcuts.

## Conditional Config (Work vs Personal)

Use different identities per directory. Common for separating work
email from personal:

```ini
# ~/.gitconfig
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

[includeIf "gitdir:~/personal/"]
    path = ~/.gitconfig-personal
```

```ini
# ~/.gitconfig-work
[user]
    email = you@company.com
    signingkey = ...
```

No more committing with the wrong email and rewriting history later.

## Commit Signing

Many projects (and most companies) want signed commits.

```ini
[commit]
    gpgsign = true

[gpg]
    format = ssh          # sign with an SSH key, simplest modern option

[user]
    signingkey = ~/.ssh/id_ed25519.pub
```

SSH signing (vs GPG) is far less painful to set up. GitHub, GitLab,
and Gitea all support it. Verify with `git log --show-signature`.

> Note: never disable signing with `-c commit.gpgsign=false` to dodge
> a failing hook. Fix the key setup instead.

## The `format.signOff` Question

Projects using the DCO (Developer Certificate of Origin) require a
`Signed-off-by:` trailer on every commit.

```ini
[format]
    signOff = true        # adds Signed-off-by automatically
```

Or per-commit: `git commit -s`. See
[../06-contribution/legal.md](../06-contribution/legal.md) for what the
DCO actually means.

## Git Hooks

Hooks run scripts at git lifecycle points. Local hooks live in
`.git/hooks/` (not committed). Useful ones:

| Hook | Fires | Use for |
|---|---|---|
| `pre-commit` | Before commit | Lint, format, fast checks |
| `commit-msg` | After message entered | Enforce message format |
| `pre-push` | Before push | Run tests, block WIP commits |

Because `.git/hooks/` isn't committed, teams use a manager so hooks
are shared and versioned. See [local-ci.md](local-ci.md) for
`pre-commit` (the framework) and friends.

### A minimal pre-push that blocks fixup commits

```bash
#!/bin/sh
# .git/hooks/pre-push
if git log @{push}.. --oneline | grep -qE '^[0-9a-f]+ (fixup|squash|WIP)'; then
    echo "Refusing to push fixup/WIP commits. Rebase first."
    exit 1
fi
```

> Hooks should be fast. A slow `pre-commit` trains you to use
> `--no-verify`, which defeats the point.

## Useful One-Off Config

```bash
# Make `git log` show dates you can read
git config --global log.date iso

# Auto-correct typos (git stauts → git status) after a pause
git config --global help.autocorrect prompt

# Reuse a single connection for multiple pushes (faster over SSH)
git config --global ssh.variant ssh

# Treat these as binary / large; don't try to diff
# (in .gitattributes, per-repo)
*.png binary
*.lock -diff
```

## .gitattributes (Per-Repo, Committed)

Not global config, but adjacent. Controls how git treats files:

```
# normalize line endings
* text=auto

# don't show generated files in diffs by default
package-lock.json linguist-generated=true
*.snap linguist-generated=true

# mark a lockfile as non-diffable noise
Cargo.lock -diff
```

This is committed, so it applies for everyone on the repo. Great for
taming noisy diffs.

## Anti-Patterns

### Copy-pasting a 500-line gitconfig you don't understand

You'll hit a surprising behavior six months later and have no idea
which line caused it. Add config deliberately, one block at a time,
and know what each does.

### Aliasing away the learning

`git ff = push --force` is a footgun waiting to fire. Don't alias
destructive commands to short strings. If anything, alias the *safe*
variant: `git pf = push --force-with-lease`.

### Global hooks that surprise collaborators

A global `core.hooksPath` that runs your personal scripts on every
repo can break clones of projects with their own hook setup. Prefer
per-repo hook managers.

### Disabling safety to make errors go away

`pull.rebase` set to lie, signing disabled, hooks bypassed — each is a
short-term unblock that creates a long-term mess. Fix the cause.

## See Also

- [shell-and-cli.md](shell-and-cli.md)
- [local-ci.md](local-ci.md)
- [../02-navigation/git-archaeology.md](../02-navigation/git-archaeology.md)
- [../06-contribution/legal.md](../06-contribution/legal.md)
- [../07-pull-requests/commit-history-management.md](../07-pull-requests/commit-history-management.md)
