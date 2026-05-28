# Command Cheatsheet

The commands worth memorizing, in one place. Grouped by what you're
trying to *do*. Explanations and context are in the linked sections.

## Searching Code (ripgrep, fd)

```bash
rg "pattern"                       # search file contents (fast, gitignore-aware)
rg -i "pattern"                    # case-insensitive
rg -w "word"                       # whole word
rg -t py "pattern"                 # only Python files
rg -g '!test/**' "pattern"         # exclude a glob
rg -l "pattern"                    # just list matching files
rg -A 3 -B 3 "pattern"             # 3 lines of context after/before
rg "fn (\w+)" -or '$1'             # extract a capture group
rg --stats "pattern"               # match counts

fd "name"                          # find files by name (fast `find`)
fd -e rs                           # files with extension .rs
fd -H "pattern"                    # include hidden files
fd -t d "name"                     # directories only
```

See [../02-navigation/search-tools.md](../02-navigation/search-tools.md).

## Git Archaeology (the high-value ones)

```bash
# PICKAXE — find commits that added/removed a string
git log -S "functionName"          # commits where the count of the string changed
git log -S "str" -- path/          # scoped to a path
git log -G "regex"                 # commits whose diff matches a regex

# LINE HISTORY — evolution of specific lines / a function
git log -L 10,20:path/file.py      # history of lines 10–20 of a file
git log -L :funcName:path/file.go  # history of a function by name

# WHO/WHEN/WHY
git blame path/file                # who last changed each line
git blame -L 40,60 path/file       # blame a line range
git log --oneline -- path/file     # commits touching a file
git show <sha>                     # a commit's full diff
git log --oneline --graph --all    # the branch graph

# Ignore a bulk-format commit in blame
# (add its SHA to .git-blame-ignore-revs, then:)
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

See [../02-navigation/git-archaeology.md](../02-navigation/git-archaeology.md).

## Bisecting (find the commit that broke it)

```bash
git bisect start
git bisect bad                     # current commit is broken
git bisect good <sha>              # this old commit worked
# git checks out the midpoint; test, then mark good/bad, repeat
git bisect good      # or: git bisect bad

# AUTOMATED — let a script decide each step
git bisect run ./test.sh           # exit 0 = good, non-zero = bad
git bisect reset                   # done; return to where you were
```

See [../04-reproducing-issues/bisecting.md](../04-reproducing-issues/bisecting.md).

## Everyday Git

```bash
git status -sb                     # short status with branch
git switch -c feature/x            # create + switch to a branch
git restore --staged file          # unstage
git restore file                   # discard working changes (careful)
git commit --fixup <sha>           # a fixup commit for later autosquash
git rebase -i --autosquash main    # interactive rebase, autosquashing fixups
git stash / git stash pop          # shelve / restore changes
git push --force-with-lease        # safer force-push (won't clobber others' work)
git reflog                         # recover "lost" commits / undo mistakes
```

## Comparing for Review

```bash
git diff main...HEAD               # what this branch ADDED since diverging (3 dots)
git diff main...HEAD --stat        # file + line summary (size up scope)
git diff --color-moved=zebra       # distinguish moved code from new
git log main..HEAD --oneline       # commits unique to this branch
git range-diff main old new        # what changed between two versions of a branch
```

See [../11-tooling/pr-reviewing-tools.md](../11-tooling/pr-reviewing-tools.md).

## GitHub CLI (gh)

```bash
# Issues
gh issue list                      # gh issue view 123 ; gh issue create

# Pull requests
gh pr list
gh pr view 1234 --comments
gh pr diff 1234
gh pr checkout 1234                # bring the PR's branch local
gh pr create --draft --title "..." --body "..."
gh pr review 1234 --approve -b "LGTM"
gh pr review 1234 --request-changes -b "see comments"
gh pr merge --squash --auto

# CI
gh run list                        # gh run watch ; gh run rerun <id>
gh run view <id> --log-failed      # just the failing step's logs

# Repo / search
gh repo fork owner/repo
gh repo view --web
gh search code "query" --language=go
gh api repos/owner/repo/pulls/123/comments    # raw API
```

See [../11-tooling/shell-and-cli.md](../11-tooling/shell-and-cli.md).

## Running & Watching Tests (by ecosystem)

```bash
pytest -k name -x --lf             # Python: filter, stop-on-fail, last-failed
go test ./... -run TestX -race     # Go: scoped, with race detector
cargo test ; cargo check           # Rust: test ; fast type-check
npx vitest --watch                 # JS/TS: watch mode
./gradlew test --tests "Foo"       # JVM (Gradle)
ctest --test-dir build             # C++ (CMake)

watchexec -e py -- pytest          # re-run on file change (any language)
```

See [../15-language-ecosystems/](../15-language-ecosystems/).

## Debugging Quick-Reach

```bash
strace -f -e trace=open,connect ./prog    # what files/sockets is it touching?
lsof -i :8080                             # what's on this port?
py-spy top --pid 12345                    # profile a running Python process
go tool pprof <profile>                   # Go profiling
g++ -fsanitize=address,undefined ...      # C/C++ memory + UB checking
rr record ./prog && rr replay             # record once, replay (reverse-debug)
```

See [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md).

## Dependency Ownership (whose bug is it?)

```bash
npm why <pkg>            # pnpm why <pkg>      — why is this dep here?
cargo tree -i <crate>                        # inverse dependency tree
go mod why <module>
pipdeptree -r -p <pkg>
```

See [../13-hidden-knowledge/right-repo-problem.md](../13-hidden-knowledge/right-repo-problem.md).

## See Also

- [../02-navigation/search-tools.md](../02-navigation/search-tools.md)
- [../02-navigation/git-archaeology.md](../02-navigation/git-archaeology.md)
- [../11-tooling/shell-and-cli.md](../11-tooling/shell-and-cli.md)
- [pre-pr-checklist.md](pre-pr-checklist.md)
