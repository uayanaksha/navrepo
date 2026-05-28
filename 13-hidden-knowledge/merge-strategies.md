# Merge Strategies

Every project has a strong opinion about how PRs land in the main
branch — squash, merge commit, or rebase. The opinion is often
*unwritten*, and violating it annoys maintainers and can get your PR
bounced. Learn the project's preference before you tidy your history.

## The Three Strategies

| Strategy | What lands on main | History shape |
|---|---|---|
| **Squash and merge** | One commit per PR | Linear; PR = one commit |
| **Merge commit** | All your commits + a merge commit | Branching; preserves PR boundary |
| **Rebase and merge** | All your commits, replayed; no merge commit | Linear; preserves individual commits |

GitHub, GitLab, and friends expose all three as buttons; projects
usually enable only the one(s) they want.

## Squash and Merge

Your whole PR becomes a single commit on main. The most common choice
for application repos and many libraries.

- **Pros:** clean, linear history; one commit per logical change; easy
  to revert (one commit); your messy "fix typo / address review / wip"
  commits disappear.
- **Cons:** loses the individual commit granularity; the squashed commit
  message matters a lot (it's all that survives).
- **Implication for you:** your intermediate commits don't matter much —
  they'll be squashed. But the **PR title and description** become the
  commit message, so write them well.

## Merge Commit

Your branch's commits land as-is, joined by a merge commit. Preserves
the exact history and the PR as a unit.

- **Pros:** full fidelity; you can see exactly what happened and when;
  the merge commit records the PR boundary.
- **Cons:** non-linear ("railroad track") history; `git log` is busier;
  bisecting crosses merge bubbles.
- **Implication for you:** your individual commits *will* be on main
  forever. Keep them clean — each should be a coherent step, not "wip"
  noise.

## Rebase and Merge

Your commits are replayed onto main with no merge commit — linear
history *and* individual commits preserved.

- **Pros:** linear history with full commit granularity; no merge
  bubbles.
- **Cons:** rewrites commit hashes; can be fiddly with conflicts; each
  commit must ideally build/pass on its own.
- **Implication for you:** like merge-commit, your commits live on
  forever, so curate them — but there's no merge commit to hide behind.

## How to Find the Project's Preference

It's often unstated. Detect it:

```bash
# Look at the shape of recent history on the main branch
git log --oneline --graph -30

# Lots of merge commits ("Merge pull request #...")  → merge-commit project
# Purely linear, one commit per PR                    → squash project
# Linear with many small related commits per feature  → rebase project
```

Also check:
- `CONTRIBUTING.md` — sometimes states it explicitly.
- The PR template or repo settings docs.
- Whether PRs ask you to "clean up your commits before merge" (→ they
  preserve commits; rebase or merge-commit).
- Whether maintainers say "squash on merge" (→ your commits don't
  matter).

## What This Means for Your Commit Hygiene

The strategy tells you how much your commit history matters:

| Project uses | Curate individual commits? | What to perfect |
|---|---|---|
| Squash | No — they vanish | PR title + description (becomes the commit) |
| Merge commit | Yes — they're permanent | Each commit's message and atomicity |
| Rebase | Yes — they're permanent | Each commit builds & passes independently |

So before tidying with `git rebase -i`, know whether it even matters.
Spending an hour crafting a perfect 6-commit history that's about to be
squashed into one is wasted effort. See
[../07-pull-requests/commit-history-management.md](../07-pull-requests/commit-history-management.md).

## Conventional Commits and Squash

Projects that use Conventional Commits (`feat:`, `fix:`, etc.) for
automated changelogs/versioning often **squash**, and the *PR title*
must follow the convention because it becomes the squashed commit
message. A bot may even lint your PR title. See
[../06-contribution/commit-conventions.md](../06-contribution/commit-conventions.md).

## Why Projects Care So Much

History is a tool they use daily:

- **`git bisect`** is far easier on linear history (squash/rebase).
- **`git blame`** is cleaner when each line traces to a meaningful
  commit.
- **Reverting** is trivial when one PR = one commit (squash).
- **Auditing / changelog generation** depends on commit message
  discipline.

Their strategy reflects how they *use* history. Respecting it is
respecting their workflow.

## Anti-Patterns

### Force-pushing a rebase on a merge-commit project

Rewriting your branch history when the project preserves commits can
disrupt review (lost review threads) and isn't what they want. Match
their model.

### Perfecting commits that will be squashed

Crafting an elegant multi-commit story on a squash project — all of it
collapses into one commit. Put the effort into the PR description
instead.

### Messy commits on a merge/rebase project

"wip", "fix", "asdf" commits that will live on main forever because the
project preserves them. Clean them up first.

### Ignoring PR-title linting

On squash + Conventional Commits projects, a non-conforming PR title
fails a check. Read the convention; format the title.

## See Also

- [../07-pull-requests/commit-history-management.md](../07-pull-requests/commit-history-management.md)
- [../06-contribution/commit-conventions.md](../06-contribution/commit-conventions.md)
- [../07-pull-requests/pr-description.md](../07-pull-requests/pr-description.md)
- [../11-tooling/git-config.md](../11-tooling/git-config.md)
