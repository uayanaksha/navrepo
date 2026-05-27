# Draft PRs

A draft PR signals "this is a work in progress, not for merge yet."
Underused. Excellent for:

- Soliciting design feedback early.
- Sharing context while CI runs.
- Letting reviewers see direction before details.
- Holding the place ("I'm working on this; don't duplicate").

## How to Create a Draft

GitHub: when creating the PR, choose "Create draft pull request."
Or via CLI:

```bash
gh pr create --draft --title "..." --body "..."
```

To mark an existing PR as draft:

```bash
gh pr ready --undo
```

To mark a draft as ready:

```bash
gh pr ready
```

GitLab: "Mark as draft" toggle. Or prefix title with "Draft:" (older
convention).

## When to Use Draft

### As an early signal

You've started serious work. You want the maintainer / team to know.
You don't want review yet.

Open a draft. Describe your approach. Continue working.

This:
- Lets maintainers comment if they have direction concerns.
- Stops others from duplicating.
- Shows progress to interested parties.

### As an RFC

A draft PR with stub code + detailed description = informal RFC. The
diff shows the API; the description shows the design.

Reviewers can comment on the API directly (inline) or on the design
(top-level).

This works especially well in projects without a formal RFC process.

### When CI is your debugger

Sometimes you need CI to tell you something:

- A platform-specific bug you can't repro locally.
- An integration test that runs only in CI.
- A flaky test that only fails 1 in 10.

A draft PR runs CI on every push. Use it to iterate. Save reviewer
fatigue by keeping it draft until clean.

### When you want feedback on direction, not details

```markdown
## This is a draft

Looking for feedback on:
- The proposed API in `service/orders.go` (lines 12-45)
- Whether to use the strategy or visitor pattern

Code is rough; not optimizing yet. Final tests / docs will come once
direction is settled.
```

Reviewers know to read the API and skip the noise.

## What a Good Draft Looks Like

### Title

Use `[WIP]` or `[Draft]` prefix if the project convention favors it.
GitHub also shows a "Draft" badge automatically.

```
[Draft] Add OAuth PKCE support
```

### Description

Make clear what *is* and *isn't* ready:

```markdown
## Status: DRAFT

### Ready for review
- API design in `auth/oauth.go`
- Test plan

### NOT ready (don't review yet)
- Documentation
- Edge case handling
- Performance optimization

### Specific questions
- Should `OAuth.start()` take options as struct or kwargs?
- Per the issue, we have two paths; happy to align before continuing.
```

Reviewers respect the boundary.

### Commit history

In a draft, don't worry about commit message hygiene. You can squash /
rebase before marking ready.

### CI status

CI runs on drafts (usually). If you're using CI as a debugger,
acknowledge:

```markdown
CI is currently red — investigating the macOS test failure.
```

## When to Mark Ready

Move from draft to ready when:

- [ ] All planned code is written.
- [ ] Self-review complete.
- [ ] Description accurate.
- [ ] Tests added.
- [ ] CI green.
- [ ] You're ready for substantive review feedback.

Don't mark ready while you're still iterating. Wastes reviewer
attention.

## Drafts vs. Discussion-First

When deciding between:

- **Draft PR**: I have code or a clear approach; want to align.
- **Issue / discussion**: I have a problem; want to brainstorm.

Sometimes both: open the issue, link a draft when you start coding.

## Long-Running Drafts

Drafts can sit for weeks. Some hygiene:

- **Update the status section** periodically.
- **Rebase main into the branch** to stay current.
- **Don't let the description go stale.** If you change direction,
  update it.

A draft that hasn't moved in a month is signal you should reconsider:
ship it, close it, or restart.

## Draft Anti-Patterns

### Drafts that should have been issues

```markdown
## Status: Draft

What if we added pagination to the orders endpoint?
```

No code. No design. Use a Discussion or Issue.

### Drafts that should have been ready

```markdown
## Status: Draft

Done. Ready for review.
```

Mark ready, then.

### Forgetting to mark ready

A reviewer might be waiting for "ready" before reviewing. If you're
done, mark ready and ping them.

### Asking for ad-hoc review on a draft

If you want feedback on a draft, write a top-level comment requesting
it specifically. Otherwise reviewers may assume "still cooking."

## CI on Drafts

Some CI workflows skip drafts to save resources:

```yaml
# .github/workflows/...
if: github.event.pull_request.draft == false
```

If you need CI on your draft, check the workflows. You might need to
mark ready briefly for one CI run, or open a separate branch.

## Drafts and Auto-Merge

Don't enable auto-merge on a draft. It'd be ignored anyway. But mark
ready *and* enable auto-merge together, only when you're confident.

## See Also

- [pr-description.md](pr-description.md) — draft descriptions are still important
- [../10-features-refactors/proposals-and-rfcs.md](../10-features-refactors/proposals-and-rfcs.md)
- [../06-contribution/issue-vs-pr-first.md](../06-contribution/issue-vs-pr-first.md)
