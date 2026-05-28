# Maintainer Calculus

When you open a PR, you see a fix. The maintainer sees a *liability they
will own forever*. Understanding the cost/benefit they silently run is
the key to getting things merged — and to not taking rejection
personally.

## The Asymmetry

You write the code once. The maintainer maintains it indefinitely:

- They review it now.
- They keep it working through every future refactor.
- They answer the bug reports it generates.
- They can't easily remove it once users depend on it.
- They explain it to the *next* contributor who touches that area.

Your one-day contribution can be a multi-year obligation for them. That
asymmetry explains almost every "no" you'll ever get.

## What the Maintainer Weighs

Every PR gets run through a rough mental ledger:

| Cost side | Benefit side |
|---|---|
| Review time now | Does it fix a real problem? |
| Maintenance burden forever | How many users does it help? |
| New surface area to support | Does it fit the project's direction? |
| Compatibility risk | Is it well-tested and low-risk? |
| Complexity added | Does it reduce *other* costs (tech debt)? |
| Support questions it'll generate | Is the contributor trustworthy? |

A PR merges when the benefit side clearly wins *and* the cost side is
small. Your job as a contributor is to shrink the costs and make the
benefits obvious.

## The Five Forces

### 1. Maintenance burden

Every line is a line someone maintains. Code that's clear, tested, and
conventional is cheap to maintain. Clever, untested, or novel code is
expensive. **Make your code boring** — boring is cheap to own.

### 2. Surface area

Every feature, flag, option, and public API is forever. Adding a config
option seems free to you; to the maintainer it's a permanent
combination to test, document, and support. Maintainers resist new
surface area *on principle*, often more than you'd expect. The fix that
doesn't add surface area is far easier to merge than the feature that
does.

### 3. Compatibility

Will this break existing users? A change that breaks compatibility is
enormously expensive (migration, deprecation, angry users). Maintainers
weigh backward compatibility very heavily. A backwards-compatible
version of your change merges; a breaking one waits for a major release
or gets rejected. See
[../05-fixing-issues/backwards-compatibility.md](../05-fixing-issues/backwards-compatibility.md).

### 4. Direction

Does this fit where the project is going? A technically perfect PR that
pulls the project in a direction the maintainers don't want will be
declined — correctly. This is why you check the roadmap *before*
building (see [hidden-roadmaps.md](hidden-roadmaps.md)). "Good code,
wrong direction" is a real and common rejection.

### 5. Trust

Who are you? A maintainer extends more benefit-of-the-doubt to a known,
reliable contributor than to a stranger's first PR. Trust is earned
through a track record of scoped, tested, considerate contributions.
This is why your tenth PR is easier than your first. See
[../14-advanced/building-reputation.md](../14-advanced/building-reputation.md).

## Implications for How You Contribute

Everything maintainer-friendly follows from the calculus:

- **Discuss before building** anything large — so you don't sink effort
  into a "wrong direction" rejection. See
  [../06-contribution/issue-vs-pr-first.md](../06-contribution/issue-vs-pr-first.md).
- **Keep PRs small and scoped** — lower review cost, lower risk. See
  [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md).
- **Bring tests** — they lower the maintenance burden you're imposing.
- **Prefer fixes over features** — features add surface area; fixes
  reduce cost.
- **Make it backwards compatible** — removes the biggest cost line.
- **Make it easy to say yes** — a low-cost, clearly-beneficial,
  well-explained PR is one a busy maintainer can merge in minutes.

## Why "No" Isn't Personal

When you understand the calculus, rejection reads differently:

- "We don't want to add this option" = surface-area cost too high.
- "This doesn't fit our roadmap" = direction mismatch.
- "Can you split this?" = review cost too high.
- "This would break X" = compatibility cost too high.

None of these are about *you* or even about whether your code is good.
They're cost/benefit verdicts on the *change*. See
[saying-no.md](saying-no.md) and
[../08-maintainers/disagreement.md](../08-maintainers/disagreement.md).

## When You Become the Maintainer

The calculus flips to your side eventually. As a maintainer, you'll
decline good code because the *cost* is too high — and you'll wish
contributors understood that it isn't personal. Run the ledger
explicitly and explain it kindly. See
[../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md).

## Anti-Patterns

### "But the code is correct!"

Correctness is necessary, not sufficient. A correct change can still
lose on surface area, direction, or compatibility. Argue the
cost/benefit, not just the correctness.

### Adding surface area casually

"Let's add a flag for that" feels free to the contributor and is
expensive forever for the maintainer. Default to *not* adding options.

### Ignoring the maintenance you're creating

A PR with no tests, novel patterns, and no docs hands the maintainer a
liability. Reduce the burden you're imposing before you ask them to
take it on.

### Taking cost-based rejection as a competence judgment

"We won't merge this" rarely means "your code is bad." Usually it means
"the ongoing cost exceeds the benefit *for us*." Different thing.

## See Also

- [hidden-roadmaps.md](hidden-roadmaps.md)
- [saying-no.md](saying-no.md)
- [../05-fixing-issues/backwards-compatibility.md](../05-fixing-issues/backwards-compatibility.md)
- [../07-pull-requests/pr-size.md](../07-pull-requests/pr-size.md)
- [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)
