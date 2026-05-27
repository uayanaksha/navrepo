# Search Before Filing

The most common reason an issue or PR gets a frustrated response: it's
a duplicate, or it was already considered and rejected.

## What to Search

Before filing anything:

- **Open issues** — someone is working on it.
- **Closed issues** — often more important than open. Includes "won't
  fix," "by design," "duplicate of #N."
- **Open PRs** — someone has a fix in flight.
- **Closed PRs** — possibly a "tried this; rejected" with explanation.
- **Discussions** — many projects use GitHub Discussions for open-ended.
- **Mailing list / forum / Discord history** — for projects living
  outside GitHub.

GitHub's default search is *only* open issues. Always include closed.

## How to Search Effectively

### Specific terms over generic

Bad: "bug in login"
Good: "login 500 uppercase email"

The more specific, the more likely you find the duplicate.

### Multiple phrasings

Try:
- The error message verbatim (`is:issue "TypeError: cannot read 'name'"`).
- The feature name (`is:issue OAuth PKCE`).
- The affected file (`is:issue "src/handlers/login"`).

Issue authors describe things differently than you would.

### Use GitHub search syntax

```
is:issue is:closed "uppercase email"
is:pr involves:username
is:issue label:bug created:>2024-01-01
in:title timeout
no:label
```

[GitHub's search docs](https://docs.github.com/search-github) are
worth a once-over.

### Check linked PRs

When you find a similar issue, check what PRs are linked. Often a fix
exists but hasn't been merged.

### Read the closed-with-no-fix issues

If you find a closed issue with "by design" or "won't fix," read the
explanation. The maintainer's reasoning is important; reopening it
without new information is rude.

## What "Already Filed" Looks Like

### Open and being worked on

- Recent comments.
- Assignee.
- Linked PR in progress.

Action: comment "I'd also like to see this; happy to help test."
Don't open a duplicate.

### Open but stale

- No comments in months.
- No assignee.

Action: comment "Still relevant; here's a fresh repro: …"  Often
revives the thread.

### Closed as "duplicate"

Action: comment on the **referenced** issue, not the closed one.
Provide new information if you have it.

### Closed as "won't fix" / "by design"

Action: usually don't re-open. If you genuinely think the situation
has changed, write a new issue that addresses why and links the old
one. The maintainer may still say no, but you've shown awareness.

### Closed as "fixed"

Verify the fix is in a release you're using. If you're on an older
version, upgrade first.

## When You Find Nothing

Now you can file. But:

1. **Reference your search.** Show you looked. ("I searched closed
   issues for X, Y, Z — didn't find anything.") This signals respect.

2. **Use the issue template.** If the project has one, fill it out.
   Skipping the template makes triage harder.

3. **Be specific.** See [minimal repro](../04-reproducing-issues/minimal-reproduction.md).

## When You're Not Sure It's a Duplicate

If you find something similar but not identical:

- Comment on the existing issue: "I see something similar — here's my
  case. Maintainers: should this be a separate issue?"

This puts the question to the maintainer rather than guessing.

## Search Beyond Issues

### Pull requests

Maybe someone proposed your fix. Read why it didn't merge.

### Discussions

Open-ended technical conversations. Often contains rationale for "why
we don't do X."

### Wiki / docs

Sometimes the "why" is in docs:

- "Why doesn't X library support Y?" — often answered in FAQ.

### Conference talks / blog posts

For high-profile projects, the maintainer may have publicly explained
their stance. A Google search beyond the repo helps.

### Past releases

`CHANGELOG.md` has fixed issues; sometimes your issue is fixed in an
unreleased version. Check the unreleased section.

## Time-Boxing the Search

Don't spend an hour searching for a 10-minute issue. A reasonable
budget:

- Trivial bug: 5 minutes search.
- Substantive bug: 15 minutes.
- New feature proposal: 30+ minutes (you want to know if this has been
  debated before).

## After Filing

If your filing turns out to be a duplicate:

- Acknowledge politely. "Thanks, I missed this. Closing in favor of
  #N."
- Subscribe to the original issue.
- Don't argue that yours is "different enough" unless it actually is.

## Why Maintainers Care

Duplicates are tax on the maintainer:

- Reading the same issue twice.
- Triaging it (closing as duplicate).
- Sometimes managing hurt feelings.

A 5-minute search saves the maintainer real time. Multiply by all
contributors and it's huge.

## See Also

- [issue-vs-pr-first.md](issue-vs-pr-first.md) — once you've confirmed novelty
- [contributing-md.md](contributing-md.md) — for project-specific search hints
