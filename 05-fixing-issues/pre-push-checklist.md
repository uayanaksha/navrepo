# Pre-Push Checklist

Before pushing — even to a draft PR — run through this. It costs 5
minutes and saves a lot of back-and-forth.

## The Checklist

### Build

- [ ] **The project builds cleanly.** No warnings you added.

```bash
make build
# or your project's equivalent
```

### Tests

- [ ] **The full test suite passes locally.** Not just the new test.

```bash
make test
# or pytest, cargo test, go test ./..., npm test
```

- [ ] **You added tests for the fix** (see [test-first-fixing.md](test-first-fixing.md)).

- [ ] **You didn't change tests just to make them pass.** The test
  predates your fix and should have been wrong before.

### Linting and formatting

- [ ] **Linter passes.** No new warnings.
- [ ] **Formatter applied.** Indentation, quotes, whitespace conform.

```bash
make lint
make fmt-check
```

Most projects automate these in pre-commit hooks. Use them.

### Manual verification

- [ ] **You exercised the fix end-to-end.** Not just unit tests — the
  actual feature path.

For backend bugs: hit the endpoint with the actual data.
For frontend bugs: open the page, click the thing.
For library bugs: write a script that uses the library.

Type-check and tests verify *correctness of code*. They don't verify
*correctness of feature*. Run the feature.

### Adjacent code paths

- [ ] **You checked code paths near the fix.** Would the same bug be
  present somewhere else?
- [ ] **You verified the fix doesn't break similar paths.**

For example: fixing email case-sensitivity in login — does signup also
need fixing? Did adding `.lower()` break case-preserving display
somewhere?

### Backwards compatibility

- [ ] **You didn't change public API.** Function signatures, return
  shapes, HTTP responses.
- [ ] **If you did**, you have a deprecation path or migration note.

See [backwards-compatibility.md](backwards-compatibility.md).

### Logs and debug prints

- [ ] **No `console.log`, `print()`, `dbg!()`, `fmt.Println("HERE")`
  leftovers.**
- [ ] **No commented-out code.** Delete it; git remembers.

A linter rule (`no-console`, `no-debug-print`) catches these. Use it.

### Comments and docs

- [ ] **Any new comments explain WHY, not WHAT.**
- [ ] **Public API changes have updated docstrings.**
- [ ] **If the bug had a comment misleading you, update the comment.**

### Commit messages

- [ ] **Subject line follows project convention** (Conventional
  Commits? `[component]` prefix?).
- [ ] **Body explains motivation** beyond the subject.
- [ ] **Issue reference** if applicable (`Fixes #1234`).
- [ ] **Sign-off** if project uses DCO (`Signed-off-by: ...`).

```bash
git commit -s -m "fix(auth): accept emails case-insensitively

Login was failing when users entered email with uppercase characters
because find_user_by_email used a case-sensitive query. Normalize
email at the boundary.

Fixes #4521"
```

### Branch state

- [ ] **Branch is up-to-date with main.**

```bash
git fetch upstream
git rebase upstream/main
# or, per project convention:
git merge upstream/main
```

- [ ] **No merge conflicts.**
- [ ] **No accidentally committed files** (env files, debug output,
  editor configs).

```bash
git status
git diff --stat main...HEAD
```

### CI mental model

- [ ] **You know what CI will check.** Read the workflow file.
- [ ] **You ran the same checks locally where feasible.**

If CI checks something you can't run locally, that's fine — but expect
to push, wait, possibly re-push.

## Optional But Recommended

### Self-review

- [ ] **You opened your own PR diff** and read it as a stranger would.
- [ ] **Added inline comments** where the diff would prompt "why?"
- [ ] **Cleaned up anything embarrassing.**

This single step often saves a review cycle.

### Performance check

For changes in performance-sensitive code:

- [ ] **You ran benchmarks** before and after.
- [ ] **You compared.** A 5% regression is worth flagging.

### Security check

For changes touching auth, input handling, or external calls:

- [ ] **You considered injection risks.** SQL, command, template.
- [ ] **You considered data leak risks.** What's logged? Returned in
  errors?
- [ ] **You considered authorization.** Does the right user have access
  to the right thing?

### Visual check (UI)

For frontend changes:

- [ ] **You opened the page in a browser.**
- [ ] **You tried at least two viewport sizes** (mobile + desktop).
- [ ] **You checked dark mode** if the project supports it.
- [ ] **You verified no console errors.**
- [ ] **You attached screenshots / video to the PR.**

## When To Skip Steps

For truly trivial PRs (typo, docs):

- Lint and build still apply.
- "Manual verification" reduces to "read the file."
- Test additions may not apply.

But err on the side of running the checklist. Skipping is how
embarrassing bugs ship.

## A Quick Sanity Run

A compressed version, when you're moving fast:

```bash
make fmt && make lint && make test
git diff main...HEAD
gh pr view 2>/dev/null  # or read your in-editor PR
```

These three commands catch 90% of pre-push errors.

## After Pushing

Once pushed:

- [ ] **Watch CI start.** First check it didn't immediately fail on
  setup.
- [ ] **If CI fails**, read the failure. Fix it before requesting
  review.
- [ ] **Don't request review on red CI** unless explicitly asking the
  reviewer for help with CI.

## When Reviewer Asks "Did You Test This?"

If you went through this checklist, the answer is yes. If you can't
honestly answer yes, run the checklist before responding.

## Anti-Patterns

- **"Tests pass on my machine, ship it."** Run them where they
  matter.
- **Pushing with `--no-verify` to skip hooks.** The hooks were there
  on purpose.
- **Multiple commits of "fix tests" "fix lint" "fix CI" — squash if the
  project uses non-squash merge; otherwise they pollute history.

## See Also

- [test-first-fixing.md](test-first-fixing.md) — tests as part of the fix
- [../07-pull-requests/self-review.md](../07-pull-requests/self-review.md) — extended self-review
- [../11-tooling/local-ci.md](../11-tooling/local-ci.md) — automating these checks
