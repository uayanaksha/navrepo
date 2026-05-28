# Be the Contributor You Wish You Had

The golden rule of open source, in one line: **act like the contributor
a tired maintainer would be grateful to receive.** Almost every good
habit in this manual falls out of taking that seriously.

## The Maintainer's Reality

Before you open an issue or a PR, picture who receives it:

- Often volunteers, unpaid, doing this in evenings and weekends.
- Drowning in notifications — issues, PRs, mentions, security reports.
- Context-switching between dozens of threads, none of which they
  asked for today.
- Burning out at a measurable rate (see
  [../13-hidden-knowledge/burnout.md](../13-hidden-knowledge/burnout.md)).

Your contribution lands in *that* inbox. The question that should guide
everything: **am I making this person's day easier or harder?**

## What the Contributor You Wish You Had Does

Flip it around. Think of the best bug report or PR *you've* ever
received as a maintainer (or imagine it). It probably:

- **Came with a reproduction.** Not "it's broken" but "here's exactly
  how to see it." See
  [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md).
- **Showed the reporter had searched first.** Not a duplicate of three
  open issues. See
  [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md).
- **Respected the templates and conventions.** Filled out the issue
  form, followed `CONTRIBUTING.md`.
- **Was scoped tightly.** One problem, one fix, easy to review. See
  [no-drive-bys.md](no-drive-bys.md).
- **Was patient.** Didn't ping after three hours. Assumed good faith.
- **Did the boring parts.** Tests, docs, a clear description — so the
  maintainer didn't have to ask.
- **Made the merge low-risk.** Backwards compatible, well-tested, easy
  to revert.

None of this is advanced. It's just *consideration*, made concrete.

## The Empathy Is Practical, Not Just Nice

This isn't only about being kind (though it is that). It's the most
effective strategy for getting your work merged:

- A maintainer with limited time merges the *easy* PRs first. Easy =
  scoped, tested, clearly explained.
- Trust compounds. The contributor who's been considerate ten times
  gets the benefit of the doubt on the eleventh.
- Friction kills contributions. The PR that's hard to review sits;
  the one that's a pleasure to review merges.

Being easy to work with is a *competitive advantage* as a contributor.

## Apply It to Future-You, Too

The contributor you wish you had also writes for the *next* person —
who is often you in six months:

- Clear commit messages that explain *why* (git archaeology will thank
  you).
- A PR description that captures the context, so the decision is
  recoverable later.
- Code written for the reader, not the writer.

"Be the contributor you wish you had" extends across time, not just
across people.

## When You're the Maintainer

The rule runs both directions. The maintainer others wish they had:

- Responds, even if it's "thanks, I'll look next week."
- Says no kindly and early, not by silence. See
  [../13-hidden-knowledge/saying-no.md](../13-hidden-knowledge/saying-no.md).
- Writes the `CONTRIBUTING.md` that would have helped them start.
- Reviews with specifics, not vague disapproval. See
  [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md).
- Thanks people for their time.

Most contributors become maintainers of *something* eventually — a
library, a module, a team's code. The empathy you practiced as a
contributor is the empathy you'll need as a maintainer.

## The Reciprocity Loop

Communities run on reciprocity. The person who shows up considerately,
helps others, reports good bugs, and reviews when asked builds a web of
goodwill that pays back over years. The person who extracts (demands
fixes, ignores conventions, pings impatiently) burns it. See
[../14-advanced/building-reputation.md](../14-advanced/building-reputation.md).

You don't have to be a saint. You have to be the kind of contributor
*you'd* be glad to see in your own inbox.

## Anti-Patterns

### Treating maintainers as a support desk

Demanding, entitled, deadline-imposing behavior toward unpaid
volunteers. They owe you nothing; they're giving you software for free.

### Optimizing for your convenience over theirs

Dumping an unscoped PR, skipping the template, omitting the
reproduction — pushing your work onto them. Do the boring parts
yourself.

### Performative consideration

Being polite in words while ignoring everything that actually makes you
easy to work with. Consideration is in the reproduction and the scope,
not the pleasantries.

## See Also

- [no-drive-bys.md](no-drive-bys.md)
- [../06-contribution/contributing-md.md](../06-contribution/contributing-md.md)
- [../08-maintainers/postures.md](../08-maintainers/postures.md)
- [../13-hidden-knowledge/saying-no.md](../13-hidden-knowledge/saying-no.md)
- [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)
