# Stacked PRs

When a change naturally breaks into a sequence of dependent steps,
stacking PRs lets each be reviewed independently — without waiting for
the previous to merge.

## When to Stack

Stack when:

- Total change is too big for one PR but logically sequential.
- Earlier PRs are foundational (refactor, scaffolding) and later ones
  build on them.
- You want continuous progress while review happens.

Don't stack for:

- Changes that aren't actually dependent (just open them in parallel).
- A single small change (one PR is enough).

## The Pattern

```
main
 |
 |--- PR1: extract OrderService
 |        |
 |        |--- PR2: rewire handlers to OrderService
 |                |
 |                |--- PR3: add new order type using OrderService
```

Each PR has the previous as its **base branch**, not `main`.

## Setting Up a Stack

### Manually

```bash
# PR1
git checkout main
git pull
git checkout -b refactor/extract-order-service
# ... commit, push, open PR1 with base=main

# PR2
git checkout -b feat/rewire-handlers refactor/extract-order-service
# ... commit, push, open PR2 with base=refactor/extract-order-service

# PR3
git checkout -b feat/new-order-type feat/rewire-handlers
# ... commit, push, open PR3 with base=feat/rewire-handlers
```

When opening each PR on GitHub, change the base branch to the previous
in the stack.

### Using tools

Several tools manage stacks automatically:

- **`git-spice`** (`gs`) — Charmbracelet's stack management
- **Graphite** (`gt`) — full-featured with web UI
- **`git-town`** — workflow extension
- **`spr`** — Spotify's stacked PR tool

These automate:
- Rebasing downstream PRs when upstream changes.
- Detecting merge of upstream PRs and re-targeting.
- Visualizing the stack.

Example with `gt`:

```bash
gt branch create refactor-order-service
# ... work, commit
gt branch submit              # creates PR
gt branch create feat-rewire  # branches from current
# ... work, commit
gt stack submit               # submits all in stack
```

## Describing a Stack

In each PR, signpost the stack:

```markdown
## Stack
1. [#1234](https://github.com/.../pull/1234) Extract OrderService **(this PR)**
2. [#1235](https://github.com/.../pull/1235) Rewire handlers
3. [#1236](https://github.com/.../pull/1236) Add new order type

This PR is foundational; review independently. Merging this unblocks
the rest.
```

Reviewers know what they're looking at and how it fits.

## Reviewing a Stack

For reviewers, a well-managed stack is:

- Each PR small and focused.
- Each readable on its own (or with brief context from the previous).
- Each mergeable when approved.

Bad stacks force reviewers to read PR1 to understand PR2. Avoid that.

## Updating a Stack

When `main` moves:

```bash
git checkout main
git pull
git checkout refactor/extract-order-service
git rebase main
git push --force-with-lease

git checkout feat/rewire-handlers
git rebase refactor/extract-order-service
git push --force-with-lease

git checkout feat/new-order-type
git rebase feat/rewire-handlers
git push --force-with-lease
```

Tools automate this:

```bash
gt sync                # graphite
gs stack restack       # git-spice
```

## When the Bottom of the Stack Merges

When PR1 merges:

```bash
# update local main
git checkout main
git pull

# re-target PR2 to main
# In GitHub, change base from refactor/extract-order-service to main
# (some tools do this automatically)

# rebase PR2 onto new main
git checkout feat/rewire-handlers
git rebase main
git push --force-with-lease

# same for PR3
git checkout feat/new-order-type
git rebase feat/rewire-handlers  # still the base until PR2 merges
# or rebase --onto main feat/rewire-handlers if PR2 already merged
```

GitHub auto-changes the base if you delete the upstream branch — but
the tooling assumes you re-target manually.

## Common Pitfalls

### Reviewer can't tell which is which

If your stack PRs have similar titles ("OrderService refactor 1/3",
"OrderService refactor 2/3"), reviewers get lost.

Use distinct names:

- "Extract OrderService class"
- "Use OrderService in handlers"
- "Add NewOrderType via OrderService"

### Merge conflicts in the stack

When PR2 has merge conflicts after PR1 lands, fix them in PR2. Don't
push the conflict to PR3 by skipping PR2.

### Stacking too deep

3–5 PRs is manageable. 10 is unmanageable. If you find yourself with
a deep stack, consider merging the early PRs first before adding more.

### Stacking unrelated work

If PR3 doesn't actually depend on PR2, separate them. Stack only what
must be sequential.

### Force-pushing breaks reviewer context

Force-pushing in a stack (necessary after rebase) loses inline
review comments. Communicate:

> "Force-pushed after rebasing on main. The substantive diff is
> unchanged; only the base merge commit shifted."

## Stacking and CI

Each PR runs CI independently. Important:

- PR2's CI runs against PR2's branch (which includes PR1's changes).
- If PR1 is broken, PR2's CI is affected.

Watch CI on the whole stack, not just the current PR.

## Splitting Mid-Stream

You started a PR, realized halfway it's actually a stack:

1. **Identify the natural break points.**
2. **Create the first branch** at a state where everything builds /
   passes.
3. **Open PR1.**
4. **Create the second branch** from PR1.
5. **Continue the rest.**

This requires careful use of `git rebase -i` or `git cherry-pick`.

## Long-Running Stacks

A stack open for weeks gets out of date constantly. Mitigation:

- Keep individual PRs small so each merges fast.
- Don't add new PRs to a stack until older ones merge.
- Use tooling to automate the rebases.

If a stack lives for months, something is wrong — usually the
maintainer doesn't want the direction. Discuss before continuing.

## See Also

- [pr-size.md](pr-size.md) — why stacking exists
- [long-running-prs.md](long-running-prs.md)
- [commit-history-management.md](commit-history-management.md)
