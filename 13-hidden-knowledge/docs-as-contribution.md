# Docs as Contribution

Documentation is the most underrated, highest-leverage, lowest-risk way
to contribute — and the fastest path to a maintainer's trust. If you're
new to a project, this is where to start.

## Why Docs Are High-Leverage, Low-Stakes

| Property | Why it matters |
|---|---|
| **Low risk** | A docs typo fix can't take down production |
| **Easy to review** | Maintainers approve clear doc fixes fast |
| **High impact** | One fixed setup step unblocks thousands of future users |
| **You're the ideal author** | As a newcomer, you just hit the gaps a veteran can't see anymore |
| **Trust-building** | A few merged doc PRs make you a known, welcome contributor |

A code PR is a liability the maintainer must evaluate (see
[maintainer-calculus.md](maintainer-calculus.md)). A good docs PR is
almost pure upside — low cost, clear benefit. Maintainers love them.

## The Newcomer's Unfair Advantage

The best person to document the onboarding experience is someone *going
through it right now*. Veterans suffer the "curse of knowledge" — they
can't see what's missing because they already know it. You can.

So as you set up the project for the first time (see
[../01-orientation/build-and-run.md](../01-orientation/build-and-run.md)),
**keep a log of every place you got stuck:**

- A setup step that was missing or wrong.
- A prerequisite the README didn't mention.
- An error you hit and how you resolved it.
- A term used without explanation.
- An example that didn't run as written.

Each of these is a ready-made docs PR. The friction you just felt is the
contribution. By tomorrow you'll have normalized it and forgotten — write
it down *now*.

## High-Value Documentation Targets

In rough order of leverage:

1. **Setup / getting-started gaps.** The first-run experience is where
   you lose the most users. A missing dependency or wrong command here
   blocks everyone. Highest leverage there is.
2. **Runnable examples.** Examples that actually work, copy-paste-able.
   Outdated examples are worse than none.
3. **Error message explanations.** "If you see X, it means Y, do Z."
   Saves countless support issues.
4. **Conceptual overviews.** The "how does this fit together" doc that's
   often missing. Hard to write, hugely valuable.
5. **API reference gaps.** Undocumented parameters, missing return-value
   descriptions.
6. **Migration guides.** How to move between versions.
7. **Troubleshooting / FAQ.** Common problems in one place.

## Doing It Well

Docs contributions still follow the contribution rules:

- **Read `CONTRIBUTING.md`.** Some projects have docs-specific
  guidelines, a style guide, or a separate docs repo.
- **Match the existing voice and structure.** Don't impose your style on
  their docs.
- **Keep PRs focused.** One fix or one section per PR — same scope
  discipline as code (see
  [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md)). Don't
  rewrite the whole docs site in one PR.
- **Verify everything you write.** A docs example that doesn't run is a
  bug. Actually run the commands you document.
- **Fix the cause when you can.** If the docs are confusing because the
  *code's* error message is confusing, consider fixing the message too
  (separately).

## Docs Reveal the Codebase

Writing docs is also one of the best ways to *learn* a codebase. To
explain something, you must understand it — so documenting a subsystem
forces the deep reading that makes you competent in it. The doc PR and
your own understanding grow together. It's learning that produces a
merged contribution as a side effect.

## Beyond the README

Documentation contribution is broader than prose files:

- **Code comments** explaining non-obvious *why* (sparingly — see the
  comment guidance in the manual's style).
- **Docstrings / API docs** that generate reference material.
- **Example projects** demonstrating real usage.
- **Blog posts / tutorials** (often welcomed and linked by maintainers).
- **Improving error messages** (docs that appear exactly when needed).
- **Answering questions** in issues/discussions (docs-in-the-moment that
  later become FAQ entries).

## The Trust Trajectory

A realistic first-month arc on a new project:

```
Week 1: Fix a broken setup step you hit.            (merged, you're known)
Week 2: Add the missing example you wished existed.  (merged, trusted more)
Week 3: A small code fix in an area you now know.    (reviewed favorably)
Week 4: A real feature/bugfix, with credibility.     (taken seriously)
```

Docs are the on-ramp. By the time you submit code, the maintainers know
your name, know you're careful, and review you generously. See
[../14-advanced/building-reputation.md](../14-advanced/building-reputation.md).

## Anti-Patterns

### Treating docs as beneath you

"I'm an engineer, I write code." Docs are often *more* impactful than
code and far easier to land. Ego here just costs you the easiest wins.

### The giant docs-rewrite PR

A 2,000-line "I reorganized everything" PR is as hard to review as a
giant code PR, and more likely to clash with the maintainers' vision.
Small, focused doc PRs.

### Documenting without verifying

Writing setup steps you didn't actually run, or examples you didn't
execute. A wrong doc is worse than a missing one. Test it.

### Forgetting your own onboarding friction

The single best source of doc contributions evaporates within days as
you acclimate. Capture it on day one.

## See Also

- [maintainer-calculus.md](maintainer-calculus.md)
- [../01-orientation/build-and-run.md](../01-orientation/build-and-run.md)
- [../06-contribution/contributing-md.md](../06-contribution/contributing-md.md)
- [../12-mindset/be-the-contributor.md](../12-mindset/be-the-contributor.md)
- [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)
