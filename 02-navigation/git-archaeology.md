# Git Archaeology

Git history is the **time dimension** of your code. Reading code without
reading history is reading half the picture.

## The Four Power Commands

Memorize these. They handle 80% of historical investigation.

### `git blame` — who, when, why

```bash
git blame path/to/file.go
git blame -L 100,120 path/to/file.go        # only specific lines
git blame --ignore-revs-file .git-blame-ignore-revs path/to/file.go
```

Yields one line per file line: SHA, author, date, line text.

Once you have a SHA, dig further:

```bash
git show <sha>                              # the full commit
git show --stat <sha>                       # files changed
git log -1 <sha>                            # just the message
```

The commit message usually has the *why*. Look for issue/PR refs and
follow them.

### `git log -S` — pickaxe

The **pickaxe** is `git log -S "string"`. It finds commits where the
count of occurrences of `"string"` changed. Essentially: when was this
added or removed?

```bash
# When was "FooBar" added or removed?
git log -S "FooBar" --all --oneline

# When was it removed, specifically?
git log -S "FooBar" --all --diff-filter=D --oneline

# Show diffs too
git log -S "FooBar" -p --all
```

This is **drastically underused**. Whenever you wonder "where did this
go?", reach for pickaxe.

### `git log -G` — regex pickaxe

When `-S` is too narrow (you need a pattern, not a fixed string):

```bash
git log -G 'func.*Foo' --all
```

Slower than `-S` because it runs regex on every diff. Use `-S` when you
can.

### `git log -L` — function history

Track a specific function (or line range) through history:

```bash
# Track a function called ProcessOrder
git log -L :ProcessOrder:internal/orders/process.go

# Track lines 100-120 of a file
git log -L 100,120:path/to/file.go

# Show with patches
git log -L :ProcessOrder:internal/orders/process.go -p
```

Works best in languages where git can detect function boundaries (Go,
C, JS). For others, use line ranges.

## Investigations You Can Now Do

### "Why is this line here?"

```bash
git blame -L <line>,<line> <file>
# get SHA, then:
git show <sha>
# read commit message; follow issue/PR refs
```

### "When was this feature added?"

```bash
git log -S "feature_flag_name" --reverse --all | head -10
# the first commit is the addition
```

### "When was this removed and why?"

```bash
git log -S "RemovedFunctionName" --all --diff-filter=D --oneline
git show <sha>
```

### "Which commits touched this function?"

```bash
git log -L :FunctionName:path/to/file.go --oneline
```

### "What's the history of this file, including renames?"

```bash
git log --follow --oneline path/to/file.go
```

The `--follow` flag detects renames. Without it, you only see commits
since the current name was assigned.

### "Who knows this code best?"

```bash
git log --format='%an' path/to/file.go | sort | uniq -c | sort -rn | head
# Top names are the people to ask if you have questions
```

Note: be careful with this. "Most lines touched" isn't always "knows best."
Sometimes the original author left and someone else became the de facto
maintainer.

### "What did this file look like a year ago?"

```bash
git show HEAD@{1.year.ago}:path/to/file.go
# or
git log --until="1 year ago" -1 --format='%H' -- path/to/file.go
# then `git show <sha>:path/to/file.go`
```

## `git bisect` — Find the Breaking Commit

When something "used to work":

```bash
git bisect start
git bisect bad                      # current HEAD is broken
git bisect good v1.4.0              # this version worked

# git checks out a midpoint commit
# test it manually, then:
git bisect good      # if it works
git bisect bad       # if it doesn't

# repeat until git identifies the culprit
```

### Automated bisect

The big upgrade: a script that returns 0 (good) or non-zero (bad):

```bash
# repro.sh — exits 0 if good, non-zero if bug present
#!/usr/bin/env bash
go test ./pkg/affected/... > /dev/null

# then:
git bisect start HEAD v1.4.0
git bisect run ./repro.sh
```

Git runs the script on each candidate commit. In ~log₂(N) iterations,
it points to the exact bad commit.

### `git bisect skip`

If a midpoint commit can't be tested (e.g., doesn't build), `git bisect
skip` tells git to try a neighbor. Useful in messy histories.

## `git rerere` — Reuse Recorded Resolution

Once enabled:

```bash
git config --global rerere.enabled true
```

…git remembers how you resolved a conflict. The next time the same
conflict appears (e.g., when you rebase a long-running branch), git
resolves it automatically.

Underused. Turn it on once and forget.

## Conventional Commits and Why It Helps

Projects using Conventional Commits (`feat:`, `fix:`, `BREAKING CHANGE:`)
give you:

- Cleaner `git log` reading.
- Auto-generated changelogs.
- Reliable filters: `git log --grep '^fix:'`.

If a project follows them, match the style. If they don't, don't unilaterally
introduce them.

## Reading Merge Commits

In merge-commit projects:

```bash
git log --merges --oneline -20
```

Each merge commit corresponds to a PR. The subject is usually
"Merge pull request #N…" — go read PR #N for context. The actual diff:

```bash
git show <merge-sha>
```

…shows the combined changes, useful for "what landed."

## The `.git-blame-ignore-revs` File

When a project does a big formatting change, blame for every line points
to that commit, hiding real history. Solution:

```bash
# .git-blame-ignore-revs (in repo root)
# Massive formatting reformat
abc123def456...

# Then configure:
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

Now `git blame` skips those revs and shows real history.

If a project has this file, *use it*. If it should and doesn't, that's
a great drive-by docs PR.

## Reflog: Your Local Time Machine

`git reflog` shows every HEAD movement in your local repo for ~90 days.
Even after a "destructive" command, content usually still exists.

```bash
git reflog
# d3adb33f HEAD@{0}: reset: moving to HEAD~3
# c0ffee01 HEAD@{1}: commit: My lost work
```

Recover:

```bash
git checkout c0ffee01
# or restore as a branch:
git branch recovered c0ffee01
```

This is why most "I lost my work" panics are recoverable.

## Anti-Patterns

- **Reading `git log` without understanding refspec.** `git log main..feature`
  is "commits in feature not in main" — very different from `git log main feature`.
- **Trusting blame on formatted code.** Use `.git-blame-ignore-revs`.
- **Squash-merging projects** — full per-commit history is often lost on
  main. Look at PR history on GitHub, not just `git log`.
- **Bisecting without isolation.** Make sure your `repro.sh` is
  deterministic, doesn't depend on uncommitted state, and exits cleanly.

## See Also

- [search-tools.md](search-tools.md) — text search across current code
- [lsp-navigation.md](lsp-navigation.md) — symbol search
- [../11-tooling/git-config.md](../11-tooling/git-config.md) — useful git config and aliases
