# Red Flags

Warning signs worth noticing — both in a repository you're evaluating
(should I depend on this? contribute here?) and in your own behavior
(am I about to make a mistake?). Spotting them early saves pain.

## Red Flags in a Repo You're Evaluating

### Project health

- **No commits in a long time** with open, unanswered issues piling up —
  possibly abandoned (see [../01-orientation/project-pulse.md](../01-orientation/project-pulse.md)).
- **Bus factor of one** — all recent work from a single person; fragile
  (see [../13-hidden-knowledge/burnout.md](../13-hidden-knowledge/burnout.md)).
- **Issues closed by a stale-bot**, not by humans — nobody has capacity
  to triage.
- **Long-open PRs with no maintainer response** — contributions go in,
  nothing comes out.
- **A "looking for maintainers" notice** — the loudest signal of trouble.

### Code & process

- **No tests**, or a test suite that doesn't pass on a fresh clone.
- **No CI**, or CI that's been red for a long time.
- **No CONTRIBUTING.md** and no visible process — unclear how (or
  whether) to contribute.
- **Last release was years ago** but the README claims active
  development.
- **Wildly inconsistent style** with no formatter — bikeshedding and
  churn ahead.
- **Huge, unexplained commits** ("update", "fixes") — poor history,
  hard to archaeology.

### Legal & governance

- **No LICENSE** — you have *no* rights to use the code, despite it being
  public (see [../13-hidden-knowledge/license-math.md](../13-hidden-knowledge/license-math.md)).
- **A source-available license** (BSL/SSPL) you mistook for open source —
  commercial restrictions apply.
- **A broad CLA** on a single-company project — relicense risk (see
  [../13-hidden-knowledge/open-core-dynamics.md](../13-hidden-knowledge/open-core-dynamics.md)).
- **Recent license change** or signals of one coming (funding pressure,
  acquisition).

### Community

- **Hostile maintainer tone** in issues/PRs — contributing will be
  unpleasant.
- **Unanswered "how do I..." questions** — no support, you're on your
  own.
- **A toxic or absent community** around the project.

> None of these is automatically disqualifying — a small, slow,
> one-maintainer project can be perfectly good. But weigh them honestly
> before you build something critical on it or invest heavy contribution
> effort.

## Red Flags in Your Own Behavior

These are the moments to stop and reconsider — you're about to create a
problem.

### In your changes

- **Your diff keeps growing** beyond the original goal — scope creep (see
  [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)).
- **You can't explain a line** you're committing — especially
  AI-generated (see [../11-tooling/ai-tools.md](../11-tooling/ai-tools.md)).
- **You're fixing the symptom** to make the error go away without
  understanding the cause (see [../05-fixing-issues/root-cause-vs-symptom.md](../05-fixing-issues/root-cause-vs-symptom.md)).
- **You're deleting code you don't understand** because it "looks
  unnecessary" (see [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md)).
- **You're about to bypass a safety check** — `--no-verify`, disabling a
  test, `git push --force` to a shared branch. Stop; find the real fix.
- **You're optimizing without having profiled** (see
  [../12-mindset/premature-optimization.md](../12-mindset/premature-optimization.md)).

### In your communication

- **You're about to reply while angry** — wait the hour (see
  [../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)).
- **You're defending every review comment** instead of considering them.
- **You're pinging impatiently** — daily "any update?" (see
  [../08-maintainers/ping-etiquette.md](../08-maintainers/ping-etiquette.md)).
- **You're relitigating a "no"** past the point of making your case once
  (see [../13-hidden-knowledge/saying-no.md](../13-hidden-knowledge/saying-no.md)).
- **You're commenting to be seen**, not to move things forward (see
  [../12-mindset/activity-vs-progress.md](../12-mindset/activity-vs-progress.md)).
- **You're treating a volunteer maintainer as a support desk.**

### In your process

- **You're about to open a large PR** with no prior discussion (see
  [../06-contribution/issue-vs-pr-first.md](../06-contribution/issue-vs-pr-first.md)).
- **You're filing a security bug publicly** — never (see
  [../13-hidden-knowledge/security-disclosure.md](../13-hidden-knowledge/security-disclosure.md)).
- **You haven't run the tests** but you're about to push.
- **You're asserting confidence you don't have** to seem competent (see
  [../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md)).
- **You're rubber-stamping a review** you didn't actually read (see
  [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)).

## The Meta Red Flag

The biggest one: **you feel rushed and you're about to skip a step you
know matters.** Reproducing the bug, reading the diff, running the tests,
waiting before replying. The shortcut almost always costs more than the
step would have. When you notice the urge to skip, that's the signal to
slow down.

## See Also

- [../01-orientation/project-pulse.md](../01-orientation/project-pulse.md)
- [../13-hidden-knowledge/maintainer-calculus.md](../13-hidden-knowledge/maintainer-calculus.md)
- [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)
- [pre-pr-checklist.md](pre-pr-checklist.md)
