# Note-Taking

A scratchpad is the cheapest, highest-leverage tool you don't think of
as a tool. Most developers re-read the same file four times because
they didn't write down what they learned the first three.

## What a Scratchpad Is

A plain text file, owned by you, *outside* the project's git tree.

```
~/notes/
  projectA/
    main-flow.md
    todo.md
    questions.md
    snippets.md
  projectB/
    ...
```

Or one big file per project. Or a notes app. Form doesn't matter —
*using it* does.

## What to Write

### 1. Current task

What are you actually trying to accomplish, in one sentence? Write it
at the top. Re-reading this 30 minutes into a deep dive saves you from
yak-shaving.

### 2. What you've learned

As you discover things:

```
- Order processing entry: handlers/orders.go:Place
- Calls orderService.Process which lives in services/orders.go
- Validation happens IN the handler, not the service (surprising)
- Repository pattern, but inventory bypasses repo for performance
```

These notes compound. Next month's session continues where this one
ends.

### 3. Open questions

```
? Why does inventory bypass the repo layer? Performance? Locking?
? Is the lock in line 87 protecting the cache or the DB?
? What's the retry policy when payment.Charge times out?
```

You don't have to answer immediately. Sometimes the answer surfaces
later; sometimes you ask a maintainer.

### 4. Hypotheses

```
HYPOTHESIS: the "duplicate order" bug happens when the client retries
            before the server commits. Likely missing idempotency key.
```

State your guess explicitly. Then test it. You'll be wrong sometimes,
and that's data.

### 5. Decisions

```
DECISION: not going to refactor the validation; out of scope for this PR.
          Will open issue #N to track.
```

When you decide not to do something, write it down. Otherwise it haunts
your PR diff.

### 6. Snippets

Commands you'll re-run:

```bash
# repro the failing case
docker compose up -d
curl -XPOST localhost:8080/orders -d @testdata/dup-order.json

# look at logs
docker compose logs orders-svc --since=5m | grep -i 'request_id=xyz'
```

Future-you will thank present-you for not having to remember.

## When to Write

- **At the start of a session** — what am I doing?
- **After every "aha" moment** — capture it.
- **Before a long detour** — note the breadcrumb back.
- **At the end of a session** — what did I figure out? What's next?

Write while you're learning. Trying to remember at the end loses 70% of
what you found.

## Granularity

Too coarse:
```
- Worked on order bug.
```

Too fine:
```
- Opened orders.go.
- Read line 17.
- Pressed F12.
- Saw it goes to handler.
- ...
```

Just right:
```
- Order placement bug: duplicates appearing when client retries.
- Found that orderService.Process doesn't check for existing order ID.
- Idempotency keys exist for payments (services/payment.go:23) but not orders.
- Plan: add idempotency to orders.Place; reuse Payment's mechanism.
- Question: should the key be client-provided or server-derived from order content?
```

Capture decisions and structure, not every keystroke.

## Long-Term Notes vs Session Notes

Two layers:

### Session notes
Throwaway, focused on what you're doing right now. Delete after the PR
merges.

### Long-term notes
Worth keeping for future sessions. Examples:

- **Architecture sketch**: layers and flows in this codebase.
- **Gotchas**: weird things to remember (e.g., "tests pollute global
  state if you don't run them with `--test-threads=1`").
- **Commands**: the project-specific spells.
- **Glossary**: the project's vocabulary (especially for domain-heavy
  projects).

Keep long-term notes in a place you'll find them again (`~/notes/`,
Obsidian vault, Notion, whatever).

## The "Surprise Log"

A special long-term note: every time the codebase surprises you, write it
down.

```
SURPRISES (projectA)
- Validation in handler, not service.
- Inventory bypasses repo layer for stock checks.
- Orders.ID is sometimes nil before Save; auto-assigned in Save.
- "user.created" event fires even for failed signups (bug?).
- The job queue has at-most-once semantics, not at-least-once.
```

Surprises are where bugs live. They're also where future you will be
confused if you don't record them.

## Marking Done

When notes accumulate, mark resolved items:

```
- [x] Found duplicate-order cause: missing idempotency key
- [x] Implemented fix
- [ ] Add test for retry case
- [ ] Update docs in API.md
```

Or strike-through, or move to an "archive" section. Anything that
distinguishes "still open" from "handled."

## Notes as Communication

When you write good notes, you can:

- Paste them into PR descriptions.
- Paste them into issues.
- Send them to a teammate as "here's what I found."
- Use them as commit message material.

Notes written for yourself are often great drafts of things written for
others.

## Tooling

### Plain markdown files

The lowest-friction option. Edit in any editor. Search with `rg`.
Version with git if you want history.

### Obsidian / Logseq / Notion

For people who want links, tags, and graph views. Worth the setup if
you maintain notes across many projects over years.

### Code comments — DO NOT

Don't put your scratchpad in code comments. They commit them, they
pollute diffs, and they leak through to others.

### Sticky notes on the monitor

Better than nothing. Worse than a text file.

## Anti-Patterns

- **Notes that aren't searchable.** "Just remembering" doesn't scale.
- **Notes you never re-read.** Notes are a tool *if* you go back to
  them. If you don't, you've just been journaling.
- **Notes mixed into code.** Keep them outside the repo.
- **Notes that recreate the README.** Don't take notes on stuff you can
  re-read trivially. Take notes on what *you* figured out.

## See Also

- [reading-strategies.md](reading-strategies.md) — when to take notes
- [tolerance-for-confusion.md](tolerance-for-confusion.md) — notes as confusion management
- [../12-mindset/](../12-mindset/) — discipline of habit
