# PR Reviewing Tools

Reviewing a PR in the GitHub web diff is fine for ten lines. For
anything real, pull it into your own tools. This page covers the
mechanics; [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)
covers the judgment.

## Size Up the Scope First

Before reading a single line, know how big the change is and where it
lands.

```bash
gh pr checkout 1234                       # bring the branch local
git diff main...HEAD --stat               # files + line counts
git diff main...HEAD --dirstat            # which directories changed most
```

The `...` (three dots) matters: `main...HEAD` shows what the branch
*added* since it diverged from main, not the noise of main moving
forward underneath it. Two dots (`main..HEAD`) is usually what you
*don't* want for review.

A glance at `--stat` tells you:
- Is this the ~200–400 line change the description claims, or 2,000?
- Does it touch the files you'd expect, or wander into unrelated dirs?
- Is it mostly generated/lockfile noise (skim) or hand-written logic
  (read carefully)?

## gh pr checkout — Review in Your Editor

The web UI can't run the code, can't use your LSP, can't let you set a
breakpoint. Pulling the branch local fixes all of that.

```bash
gh pr checkout 1234        # creates/switches to the PR's branch
```

Now you have the full power of your environment:
- Go-to-definition into the changed code and its callers.
- Find-references to see the blast radius of a changed function.
- Run the tests. Run the *branch*. Set breakpoints.
- Use `git blame` / `git log` on the surrounding code for context.

To get back: `git checkout main` (or `-`).

## GitHub's "Viewed" Checkboxes

On a multi-file PR, the per-file **Viewed** checkbox (top-right of each
file in the Files Changed tab) is the difference between a coherent
review and getting lost.

- Check a file once you've fully reviewed it; it collapses.
- If the author pushes changes, only *changed* files re-expand and
  un-check — so you re-review just the delta, not everything.
- Your progress persists across sessions.

For a 30-file PR, this turns an overwhelming wall into a checklist you
can put down and pick up.

## Read the Description and Tests First

Order of operations for a substantive review:

1. **The PR description.** What problem, what approach, what's
   explicitly out of scope. If it's empty, that's your first comment.
2. **The tests.** They tell you what the author thinks the change
   *does*, and what they consider the contract. Often clearer than the
   implementation.
3. **The interface / signatures.** Public API, types, function
   shapes — the surface others depend on.
4. **The implementation.** Now that you know the intent, the logic
   reads fast.

Reading the diff top-to-bottom with no context is the slow way.

## Reviewing the Diff Locally

Beyond the editor, useful local diff views:

```bash
# Range-diff: compare two versions of a branch (e.g. after a force-push)
git range-diff main old-sha new-sha

# What changed between the PR's two latest pushes
git range-diff @{u}@{1} @{u}

# Just the commits, to judge history quality
git log main..HEAD --oneline

# Diff with move detection and word-level highlighting
git diff --color-moved=zebra --word-diff main...HEAD
```

`git range-diff` is the answer to "the author force-pushed; what
actually changed since my last review?" — far better than re-reading
the whole diff.

## When to Actually Run the Branch

Reading catches a lot. Some things only running catches:

- **UI / visual changes** — screenshots in the PR help, but click
  through the real thing for interaction and edge states.
- **Performance claims** — "this is faster" needs a benchmark you can
  reproduce, not a vibe.
- **Behavior you can't trace by reading** — async timing, integration
  with an external service, anything with a lot of state.
- **The reproduction in a bugfix PR** — confirm the bug existed on
  `main` and is gone on the branch.

If the PR claims to fix a bug, the highest-value review action is
often: check out `main`, reproduce the bug, check out the branch,
confirm it's fixed.

```bash
git checkout main && # reproduce...
gh pr checkout 1234 && # confirm fixed...
```

## Leaving Review Comments

```bash
# Inline / overall review from the terminal
gh pr review 1234 --comment -b "..."
gh pr review 1234 --approve -b "LGTM, one nit inline"
gh pr review 1234 --request-changes -b "see comments"

# Pull the conversation into your terminal
gh pr view 1234 --comments
```

For line-level comments, the web UI is still usually easiest. GitHub's
**suggestion** blocks (```` ```suggestion ````) let the author accept
your exact edit with one click — use them for concrete small fixes
instead of describing the change in prose.

## Reviewing in Your IDE

Editor integrations bring the review into the tool where you can
navigate:

- **VS Code**: the GitHub Pull Requests extension — check out, comment,
  approve, all in-editor.
- **JetBrains**: built-in pull request view.
- **`delta`** as your pager makes any local `git show` / `git diff`
  readable (see [git-config.md](git-config.md)).

The win is the same as `gh pr checkout`: real navigation, not a
read-only web pane.

## Anti-Patterns

### Reviewing huge PRs in the web diff

Past a few hundred lines, the web diff hides structure and kills your
navigation. Check it out locally. (Or ask the author to split it — see
[../14-advanced/reviewing-large-prs.md](../14-advanced/reviewing-large-prs.md).)

### Approving without checking out anything

For a one-line typo fix, fine. For real logic, "LGTM" after a 30-second
skim is how bugs get waved through. Your approval is a signature.

### Re-reading the whole PR after a force-push

`git range-diff` shows you exactly what changed since last time. Don't
re-spend the whole review budget on unchanged code.

### Ignoring the tests

The tests encode the author's understanding of the contract. Skipping
them means you're reviewing the implementation without knowing what
it's supposed to do.

## See Also

- [git-config.md](git-config.md)
- [shell-and-cli.md](shell-and-cli.md)
- [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)
- [../14-advanced/reviewing-large-prs.md](../14-advanced/reviewing-large-prs.md)
- [../07-pull-requests/self-review.md](../07-pull-requests/self-review.md)
