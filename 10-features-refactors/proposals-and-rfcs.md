# Proposals and RFCs

For anything beyond a trivial bug fix, **align before you build**. A
paragraph of upfront discussion saves a week of misaligned code.

## When to Propose

| Change size | Propose first? |
|---|---|
| Bug fix, small | Often no |
| Bug fix, controversial | Yes |
| Refactor, internal | Often yes (issue) |
| Refactor, public API | Yes (RFC or discussion) |
| New feature, small | Issue |
| New feature, large | RFC |
| Architecture shift | RFC + design doc |

When unsure: propose. The cost is low.

## What a Proposal Contains

### Problem statement

What's broken or missing? Who's affected?

> "Login fails for users with uppercase email characters. Affects ~2%
> of users; reported in #4521. Blocks customer X."

### Options considered

At least two, with tradeoffs.

> Option A: Normalize at the storage layer.
> - Pro: covers all callers.
> - Con: requires DB collation change for full coverage.
>
> Option B: Normalize at each call site.
> - Pro: smaller diff.
> - Con: easy to miss a caller.
>
> Option C: Migrate existing data to lowercased.
> - Pro: cleanest data.
> - Con: requires a one-time migration.

### Recommendation

Your pick and why.

> Recommend Option A. It covers all callers without data migration
> and is the smallest reversible change.

### Out of scope

Explicit boundaries.

> Out of scope: migrating historical emails (separate issue if
> needed); changing display case.

### Migration / compatibility

How are existing users affected?

> No breaking changes for clients. Existing emails stored mixed-case
> continue to work.

### Open questions

Things you haven't decided.

> Open: should signup also enforce lowercase, or just normalize at
> lookup? Leaning yes for cleanliness; happy to defer.

## RFC Formats

Different projects do it differently:

### GitHub Issue

For small-ish proposals. Add the sections above as the issue body.

### GitHub Discussion

For more open-ended ideas, especially pre-issue.

### Markdown file in repo

For projects with formal RFC processes:

```
docs/rfcs/
├── 0001-template.md
├── 0002-oauth-pkce-support.md
└── 0003-graphql-api.md
```

Each RFC numbered, dated, statused.

### External design doc

Some projects use Google Docs, Notion, etc. for collaborative editing,
then commit the final to the repo.

### Mailing list

Older projects (Linux, Postgres, Python). Format expected on the list.

Check the project's existing examples and match.

## How to Solicit Feedback

### Direct asks help

> "Specifically curious about:
> 1. Is the API shape OK?
> 2. Should this be opt-in or default?
> 3. Are there callers I'm missing?"

Numbered questions get numbered responses.

### Open-ended also works

> "Any thoughts welcome — I'll iterate."

Less directive but invites broader feedback.

### Tag relevant people

If CODEOWNERS or recent commits suggest specific people, gently tag:

> "@alice you've been working on auth lately — would value your input
> when you have a moment."

Don't tag too many; that's pinging.

## Iteration

A proposal usually goes through a few revisions:

1. Initial proposal.
2. Feedback: clarification questions, alternatives.
3. Revision: incorporate, address.
4. Second feedback round.
5. Consensus (often "OK, try it").
6. Implementation.

Two-three revisions is normal. More than five = direction is unclear;
break into smaller proposals.

## When Proposals Get Stuck

### No engagement

After 2 weeks of silence, ping politely:

> "Any thoughts? Happy to close if it's not a fit right now."

If still silent, sometimes building a draft PR helps — code makes
discussion concrete.

### Endless bikeshed

Discussion grew but consensus isn't emerging. Move to action:

> "Going to draft a PR with the leading approach (B). Easier to react
> to code than to text. Will revert direction if PR review converges
> elsewhere."

Sometimes code resolves what text can't.

### Disagreement on direction

Two camps form. Three options:

1. **Wait for a maintainer decision** — they hold the tiebreaker.
2. **Run a small experiment** — try one direction; see what happens.
3. **Build both** — sometimes both are workable; let usage decide.

Don't try to "win" the discussion. Let it resolve.

## Proposal Anti-Patterns

### Too vague

> "Should we use GraphQL?"

Versus:

> "Adding a GraphQL endpoint at /graphql for the dashboard's needs.
> Coexists with REST. Schema and resolver in PR draft #N. Estimated 2
> weeks of work."

The first invites debate. The second invites decision.

### Too tactical

A long discussion about the color of the button before agreeing
there's a button.

Discuss intent first, mechanics later.

### "Why don't we just..."

Casual suggestions in chat without follow-through. Don't drop these
on maintainers. If you propose, drive it.

### Asking permission for things you control

If it's clearly your call (your project, your specific file), don't
ask — just do.

### Asking forgiveness when you needed permission

The opposite: doing major changes unilaterally, then submitting and
hoping. Almost always fails.

## RFC Lifecycle (for Formal Processes)

```
Status: Draft       → still being discussed
Status: Proposed    → ready for decision
Status: Accepted    → approved, implementation pending
Status: Implemented → in main
Status: Rejected    → decided against
Status: Superseded  → replaced by a newer RFC
```

Each status changes how to interact with the RFC. An "Accepted" RFC
doesn't need re-debating; implementation is the next step.

## After Acceptance

Once your proposal is accepted:

1. **Acknowledge.** "Thanks; will start on the implementation."
2. **Link your PR back** to the proposal.
3. **Update the proposal** if scope changes during implementation.
4. **Note resolution** when shipped: "Implementation complete in
   #1500."

## When You're On the Other Side

If you're reviewing someone else's proposal:

- **Engage promptly.** Even "I'll think about this and reply by
  Friday" is better than silence.
- **Ask clarifying questions.**
- **Share concerns specifically.**
- **Don't bikeshed.**
- **Let consensus emerge.**

Proposals are collaborative. Be the reviewer you'd want.

## See Also

- [../06-contribution/issue-vs-pr-first.md](../06-contribution/issue-vs-pr-first.md) — the framing decision
- [../07-pull-requests/draft-prs.md](../07-pull-requests/draft-prs.md) — code-as-RFC
- [spike-branches.md](spike-branches.md) — when a proposal needs evidence
