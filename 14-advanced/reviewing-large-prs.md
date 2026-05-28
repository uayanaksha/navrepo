# Reviewing Large PRs

A 2,000-line PR is where review quality goes to die — attention fades,
and reviewers start rubber-stamping. When you can't get it split, you
need a system to review it without either drowning or waving it through.

## First: Should It Be Split?

The best review of an oversized PR is often "please split this." Before
investing hours:

- **Can it be staged?** A refactor (no behavior change) + a feature on
  top is two reviewable PRs, not one (see
  [../07-pull-requests/stacked-prs.md](../07-pull-requests/stacked-prs.md)).
- **Is it mechanical + substantive mixed?** Ask for the mechanical part
  (rename, move, format) as a separate PR so the substantive part is
  reviewable.
- **Is it genuinely atomic?** Some changes can't be split (a single
  rename rippling across files, a schema migration). Then it's large
  for a real reason — proceed with a system.

Asking for a split isn't laziness; it's protecting review quality. But
ask *early*, before the author has rebased it five times. See
[../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md).

## Read the Test Plan and Description First

On a large PR this is non-negotiable. Before any code:

1. **The description** — the map of what changed and why.
2. **The test plan** — how the author verified it. If there isn't one,
   that's your first request; you can't review 2,000 lines of unverified
   change.
3. **The commit structure** — if the author split it into logical
   commits, *review commit-by-commit*, not as one giant diff. A
   well-structured commit history turns one huge review into several
   small ones.

```bash
gh pr checkout 1234
git log main..HEAD --oneline        # is there a reviewable commit story?
git diff main...HEAD --stat         # where's the bulk? (see pr-reviewing-tools.md)
```

## Make a Pass Plan

Don't read top-to-bottom. Triage the diff into a route:

1. **Separate noise from signal.** Generated files, lockfiles, vendored
   code, mass-renames — skim or skip (verify they're really mechanical,
   then move on). Spend your attention on hand-written logic.
2. **Find the core.** The 200 lines that actually matter usually hide in
   the 2,000. The `--stat` and the description point to them. Review
   those *first and hardest*, while your attention is fresh.
3. **Then the periphery** — callers, tests, docs — once you understand
   the core.

## Review in Layers, Not One Marathon

Large PRs exceed the attention budget of a single sitting. Quality drops
sharply after the first ~400 lines. So:

- **Break it into sessions.** Review a coherent slice, mark files
  **Viewed** (see [../11-tooling/pr-reviewing-tools.md](../11-tooling/pr-reviewing-tools.md)),
  stop before you're fatigued, resume later.
- **Use the Viewed checkboxes** so you never re-read what you've cleared
  and you re-review only what the author changes after a push.
- **Take breaks between layers.** A tired reviewer rubber-stamps; a
  fresh one catches things.

## Layered Reading Order

A reliable sequence for a big change:

1. **Interfaces and signatures** — public API, types, function shapes.
   The contract.
2. **Data model / schema changes** — these have the widest blast radius
   and are hardest to change later.
3. **Core logic** — the algorithms and control flow that do the work.
4. **Tests** — do they actually exercise the new behavior and its edges?
5. **Wiring and callers** — how it connects to the rest.
6. **Docs, config, periphery** — last.

## Run It

For a large change, reading isn't enough — check it out and exercise it:

- Run the test suite on the branch.
- For a bugfix, reproduce on `main`, confirm fixed on the branch.
- For risky changes, run the actual feature, poke the edges.
- Consider a second reviewer for the highest-risk sections (security,
  migrations, concurrency).

## Manage the Human Side

A large PR represents a lot of someone's work; the review carries
emotional weight (see
[../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)):

- **Front-load the big concerns.** If the *approach* is wrong, say so
  first — don't let them fix 50 nits and *then* learn the design needs
  rework.
- **Batch your comments.** Post one coherent review, not a trickle of
  notifications over hours.
- **Distinguish blockers from nits** ruthlessly (see
  [giving-code-review.md](giving-code-review.md)) — on a huge PR, an
  unlabeled pile of comments is overwhelming.
- **Acknowledge the effort.** Large PRs are draining to author; a word
  of recognition helps.

## Anti-Patterns

### Rubber-stamping by fatigue

"LGTM" on line 1,800 because you stopped really reading at 400. If you
can't review it all with attention, review it in sessions or ask for a
split — don't approve what you didn't read.

### Reviewing top-to-bottom

Reading a 2,000-line diff linearly burns your attention on imports and
boilerplate before you reach the logic. Triage first; core first.

### Nitpicking the easy parts to feel productive

Leaving twenty comments on naming in the boilerplate while the gnarly
concurrency core gets a glance. Spend attention where the risk is.

### Demanding the split too late

Asking for a split after the author has poured days into the monolith
and rebased it repeatedly. Catch size early — ideally at the proposal
stage.

### Never running it

Approving a large, risky change purely from reading. Check it out and
exercise the dangerous parts.

## See Also

- [giving-code-review.md](giving-code-review.md)
- [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md)
- [../07-pull-requests/stacked-prs.md](../07-pull-requests/stacked-prs.md)
- [../11-tooling/pr-reviewing-tools.md](../11-tooling/pr-reviewing-tools.md)
- [../03-reading-code/reading-strategies.md](../03-reading-code/reading-strategies.md)
