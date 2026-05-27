# Bisecting Regressions

When code that worked last week is broken today, `git bisect` finds the
exact commit that caused it. Used right, it converts "where could it
be?" into "exactly here."

## The Basic Flow

```bash
git bisect start
git bisect bad                      # current HEAD is broken
git bisect good v1.4.0              # this version worked
```

Git checks out a midpoint. You test:

```bash
git bisect good     # works → bug is in newer half
git bisect bad      # broken → bug is in older half
```

Repeat. Within log₂(N) iterations, git points to the culprit:

```
abc123def is the first bad commit
commit abc123def
Author: ...
Date: ...
    Refactor order processing
```

End with `git bisect reset` to return to your starting state.

## Automated Bisect

If you can write a script that exits 0 on "good" and non-zero on "bad":

```bash
# repro.sh — exits 0 if the bug is fixed, non-zero if present
#!/usr/bin/env bash
go test -run TestSpecificBug ./...
```

Then:

```bash
git bisect start HEAD v1.4.0
git bisect run ./repro.sh
```

Git runs the script on each candidate. You walk away; come back to the
answer.

**This is the right way to bisect.** Manual bisect is for cases where
automation isn't feasible.

## Picking Good and Bad Commits

The "good" end has to actually be good. Verify:

```bash
git checkout v1.4.0
./repro.sh
# should pass (exit 0)
```

If `repro.sh` fails on the "good" commit, you have:
- A different bug.
- Or the "good" version isn't actually good.
- Or your repro depends on something that's been broken longer than you
  think.

Find an older known-good commit and start there.

## When Bisect Finds a Merge Commit

You might end up at a merge commit. Two paths:

```bash
git bisect bad <merge-sha>
# Now bisect within the merged branch
git bisect start <merge-sha> <merge-parent>
```

Or use `--first-parent` to stay on the main branch's commits only:

```bash
git bisect start --first-parent
```

…which avoids descending into the merged branch's individual commits.

## `git bisect skip` — When a Commit Can't Be Tested

Sometimes a midpoint commit doesn't build or doesn't even run. You can't
say good or bad.

```bash
git bisect skip
```

Git picks a neighbor. If many adjacent commits are unbuildable,
bisect's resolution degrades but it'll still narrow the range.

## Bisect with a Non-Test Repro

Bisect doesn't require tests. The repro can be anything:

```bash
# repro.sh
#!/usr/bin/env bash
./build.sh
./app --process testdata/input.txt > out.txt
diff -q out.txt expected.txt
```

The script's exit code is the signal. Build, run, compare.

## Bisecting Performance Regressions

For perf bugs, the repro should measure:

```bash
# repro.sh
#!/usr/bin/env bash
./build.sh
duration=$(./benchmark --runs=10)
# threshold: regression is anything over 500ms
[[ "$duration" -lt 500 ]]
```

Watch out for measurement noise — make benchmarks deterministic enough
(many runs, isolation) before bisecting on them.

## Bisecting with Side Effects

If your repro mutates external state (DB, files, services), reset
between iterations:

```bash
# repro.sh
#!/usr/bin/env bash
reset_db.sh
./trigger_bug.sh
check_result.sh
```

Or use ephemeral environments (Docker, tmpfs).

## Bisecting Across Multiple Repos

For multi-repo systems (microservices), bisect each separately:

1. Pin all other repos to known-good versions.
2. Bisect the suspected repo.
3. If clean, pin that repo and bisect another.

Tedious but tractable. Some companies have tooling for cross-repo
bisect.

## Bisect for Behavioral Changes (Not Bugs)

You can bisect any binary outcome — not just bugs. For example:

- "When did the API start returning this field?"
- "When did our binary size start growing?"
- "When did this test name appear?"

Bisect is a general "find the transition commit" tool.

## After You Find the Commit

Once bisect identifies the culprit:

1. **Read it.** What does the commit say it does? What does it actually
   change?
2. **Check the PR.** GitHub: `gh pr list --search "<sha>"` or search the
   commit message.
3. **Decide on the fix.**
   - Revert the commit (if surgical and recent).
   - Patch forward (if the commit also did good things).
   - Discuss with the author (if intentional).

**Don't revert silently.** A revert is a real change; communicate it like
one.

## Common Pitfalls

### "First bad commit is unrelated"

Sometimes bisect points at a refactor that "shouldn't" have caused the
bug. Two explanations:

1. The refactor exposed a latent bug. The "real" bug is older.
2. The refactor has subtle behavior change you haven't found.

Either way, that commit is where you start reading.

### Build flakes

If the build is non-deterministic, bisect's results are too. Make
`repro.sh` clean state explicitly.

### Test flakes

If the test you're using as repro is itself flaky, bisect lies. Run the
test multiple times to confirm reliability before starting.

### Wrong `good` baseline

If `good` isn't actually good (the bug existed there too), bisect
finds garbage. Always verify.

## Reflog Recovery

If you mess up during bisect (forgot to `git bisect reset`, or got
lost):

```bash
git bisect reset                    # back to where bisect started
git reflog                          # see all HEAD movements
git checkout <some-earlier-state>
```

## Bisect vs. Targeted Reading

Bisect is best when:
- You know "it used to work."
- You don't know which file/area is responsible.
- You have a reliable repro.

Bisect is overkill when:
- You already know the area (e.g., "broke after I added X").
- The fix is obvious from the error.
- The bug is intermittent (bisect won't give a clean answer).

## See Also

- [minimal-reproduction.md](minimal-reproduction.md) — repro is prerequisite
- [../02-navigation/git-archaeology.md](../02-navigation/git-archaeology.md) — broader git history skills
- [../05-fixing-issues/root-cause-vs-symptom.md](../05-fixing-issues/root-cause-vs-symptom.md) — what to do after finding the commit
