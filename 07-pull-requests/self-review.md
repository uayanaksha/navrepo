# Self-Review

Before you request review, **review your own PR** as a stranger would.
This single habit doubles the speed of your reviews.

## The Process

### Step 1 — Open the PR diff in the web UI

Not your editor. Not `git diff`. The actual review interface (GitHub
"Files changed," GitLab "Changes," etc.).

The web UI gives you what reviewers see — including the file
boundaries, the line numbers as they'll comment, and the absence of
your editor's helpful context.

### Step 2 — Read it linearly

Top of the file list to bottom. Each file. Each hunk.

Don't skip files. Don't skim. Read.

### Step 3 — Annotate

For each spot a reviewer might pause:

- **Leave an inline comment** explaining the "why."
- Or, if the why isn't obvious, ask yourself: should this be in a code
  comment?

Sample inline comments:

> "This nil check might look redundant — added because `get_user()` can
> return None for users mid-deletion. See #4521."

> "Chose `sync.Once` over a flag because we need exactly-once
> initialization across goroutines."

> "Naming this `_legacy` because the new path is in #4525; this
> wrapper stays until migration completes."

Reviewers will appreciate these. You're saving them from asking
questions.

### Step 4 — Remove embarrassments

While reading, clean up:

- Debug prints (`print()`, `console.log`, `dbg!()`).
- Commented-out code.
- TODO comments referring to other tasks ("TODO: refactor this later").
- Variable renames you didn't fully apply.
- Files you accidentally added.

### Step 5 — Verify your description

Does the description in your PR match what the diff actually does?

Common drift:
- You added a "test plan" item you didn't actually do.
- Your "Changes" bullets are out of date.
- You forgot to link the issue.

Update both before requesting review.

## What Self-Review Catches

### Half-applied changes

```python
def process(order):
    user_id = order.userId  # renamed elsewhere to user_id
```

Inconsistencies that LSP and tests might miss but a reviewer will
catch. Catch them first.

### Bad names

```python
def fix_thing(x):  # what's "thing"? what's x?
```

Names you wrote quickly during exploration but didn't refine. Self-
review is when you upgrade them.

### Useless comments

```python
# increment counter
counter += 1
```

Delete.

### Repeated code

The change you made in one place that should have been made in three.
Self-review often spots these patterns.

### Out-of-scope creep

You said you were fixing the login bug. Why are there changes in
`Profile.tsx`?

Either:
- Revert the out-of-scope changes (separate PR).
- Or update the description to mention them.

The PR should match its title.

## Self-Review Anti-Patterns

### Skipping it

The most common one. "I just wrote this; I know what it does." You
know what you *intended*. Self-review checks what you actually did.

### Self-review as approval

Some review tools let you "self-approve." Don't. Self-review is
about catching issues, not validating.

### Self-review only as you type

Reviewing while writing isn't the same as reviewing after. The
mental shift to "stranger reading this" requires the diff to be
complete first.

### Skipping when "the change is small"

Small PRs have small bugs. They're cheap to self-review. Do it anyway.

## Building the Habit

If you struggle to self-review:

- **Set a timer**: 5 minutes minimum on the diff before submit.
- **Walk away first**: do something else for 5 minutes; come back fresh.
- **Read out loud**: catches awkward phrasing in both code and comments.

After 20–30 reps, the habit is automatic.

## Self-Review and AI Tools

AI tools can self-review with you:

- "Review this diff for bugs."
- "What edge cases am I missing in this function?"
- "Is there a simpler way to write this?"

Use as a second pair of eyes, not as a replacement for human review or
self-review.

But: AI can hallucinate problems. Verify before changing in response
to "AI says it's a bug."

## See Also

- [../05-fixing-issues/pre-push-checklist.md](../05-fixing-issues/pre-push-checklist.md) — what to do before self-review
- [pr-description.md](pr-description.md) — keep in sync during self-review
- [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md) — the techniques you're applying to yourself
