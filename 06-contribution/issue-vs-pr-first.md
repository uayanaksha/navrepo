# Issue First or PR First?

A surprisingly consequential decision. Get it wrong and you can spend
weeks on a PR that was never going to merge.

## The Decision Matrix

| Situation | Approach |
|---|---|
| Trivial fix (typo, obvious bug) | **PR directly**, link the change |
| Small bugfix with clear scope | PR; mention in description |
| Visible behavior change | **Issue first**; PR after discussion |
| Bug whose fix is non-obvious | Issue first to align on direction |
| New feature, small | Issue or discussion first |
| New feature, large | **RFC / discussion / design doc first** |
| Refactor | **Issue first**, scope-bounded |
| Any PR > 200 lines | Discuss first, even if you think it's obvious |
| Security issue | **Private channel** per `SECURITY.md` |

## "Issue First" Defaults

For most non-trivial contributions, **issue first** is the safer
default. Reasons:

- **You learn the maintainer's stance before spending hours.** Sometimes
  it's "we don't want this" or "we already plan to do it differently."
- **You get design feedback early.** Cheaper to revise an issue than a
  PR.
- **You make scope explicit.** Reduces "you missed this case" review
  cycles.
- **You signal intent.** Other contributors won't duplicate work.

The cost is one extra round-trip. Usually worth it.

## "PR First" Defaults

PR-first works when:

- The change is small (under ~50 lines).
- The fix is obviously correct.
- The behavior change is invisible to users.
- The project has fast review and short turnaround.
- The CONTRIBUTING file says "PR is fine for small bugs."

When in doubt, default to issue first.

## What Goes in the Issue

A good issue:

### Bug report

- **Title**: specific, includes the symptom. Bad: "Bug in login." Good:
  "Login returns 500 for emails with uppercase characters."
- **Environment**: version, OS, anything relevant.
- **Steps to reproduce**: numbered, exact.
- **Expected vs actual**: clear contrast.
- **MRE if you have one**: copy-pasteable.

### Feature request

- **Use case**: who, what, why.
- **Proposed approach**: optional but helpful.
- **Alternatives considered**: shows you thought about it.
- **Out of scope**: bounds the discussion.

### Refactor proposal

- **What's wrong currently**: be specific.
- **What you'd change**: high-level direction.
- **Cost / benefit**: be honest about the cost.
- **Migration plan**: if it's breaking.

## Discussion vs Issue

Many projects use GitHub Discussions for:

- Open-ended ideas ("what should we do about X?").
- Q&A ("is there a way to do Y?").
- Pre-issue exploration.

Discussions don't pollute the issue tracker with non-actionable items.

If unsure: **Discussion for "should we?", Issue for "let's track this."**

## RFC / Design Doc

For larger changes, an RFC (Request for Comments) or design doc is
common:

- **Purpose**: forces explicit design before code.
- **Format**: varies; usually a markdown file or Discussion post.
- **Sections**: motivation, proposed design, alternatives, drawbacks,
  unresolved questions.

Examples of RFC processes:
- Rust language RFCs.
- Python PEPs.
- Many internal company RFC processes.

If your change is RFC-worthy, do the RFC. Skipping leads to PRs that
get rejected for "we'd prefer a different approach."

## Draft PR as RFC

For some projects, a **draft PR** serves as the RFC:

- Code stubbed; description has the design.
- Reviewers comment as if reviewing the design.
- Once aligned, code is filled in.

This works well when:

- The project is small and informal.
- The code-level details are part of the discussion.
- Maintainers prefer code over prose.

See [../07-pull-requests/draft-prs.md](../07-pull-requests/draft-prs.md).

## How to Ask for Direction

Sample wording for an issue:

> **Issue title**: Add OAuth PKCE support
>
> Currently `auth.OAuth` doesn't support PKCE (RFC 7636). I'd like to
> add it for mobile clients that can't safely store secrets.
>
> Approach I'm considering:
>
> - Extend `OAuth.start()` with a `pkce: bool` parameter.
> - Generate code_verifier client-side; send code_challenge in start.
> - On token exchange, send code_verifier.
>
> Would you accept a PR for this? Any preferences on the API shape?
> Happy to draft.

This invites discussion without being too tentative.

## Waiting for Response

After filing, **wait for triage** before opening a PR (when the project
expects issue first).

How long?

- For active projects: a few days.
- For maintenance projects: 1–2 weeks.
- For overloaded projects: maybe longer.

If no response after a couple weeks, a gentle ping is fine:

> "Friendly ping — happy to work on this if there's interest. Otherwise
> I'll plan to close."

## "I Already Have the Code"

A common pattern: you fixed the bug for yourself; you have the patch.
Do you open the issue or the PR?

- **For trivial fixes**: PR with a clear description of the bug.
- **For non-trivial**: issue first, mention you have a patch ready.

Posting a PR without context can come across as presumptuous. Posting
context first signals respect.

## Anti-Patterns

- **PR'ing a large feature without prior discussion.** Almost always
  results in either rejection or rework.
- **Opening 10 small issues in one day.** Looks like spam; triage burden
  is real.
- **Filing without searching.** See [search-before-filing.md](search-before-filing.md).
- **"What do you think?"-style issues with no proposal.** Maintainers
  prefer concrete proposals to brainstorm with.
- **Reopening rejected issues with new wording.** Address the
  original reason, or move on.

## "Just Do It" Cultures

Some projects prefer PRs over issues:

- Many tools (e.g., older Linux kernel subsystems via mailing list).
- Solo-maintainer libraries where the maintainer prefers code review
  over text.
- Projects with "patches welcome" cultures.

Sample their recent activity. If 90% of changes start as PRs, do the
same.

## See Also

- [contributing-md.md](contributing-md.md) — project-specific guidance
- [../07-pull-requests/draft-prs.md](../07-pull-requests/draft-prs.md)
- [../10-features-refactors/proposals-and-rfcs.md](../10-features-refactors/proposals-and-rfcs.md)
