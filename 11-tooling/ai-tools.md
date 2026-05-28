# AI Tools

AI coding assistants are real tools with real leverage and real
failure modes. This page is the honest version: where they help, where
they hurt, and the one rule that keeps you safe.

## The One Rule

**Verify everything. You own the output.**

When you commit AI-generated code, it's your code. "The AI wrote it"
is not a defense in code review, in an incident postmortem, or in your
own understanding six months later. If you can't explain it, don't
ship it.

Everything below follows from this.

## Where AI Genuinely Helps

| Task | Why it works |
|---|---|
| Explaining unfamiliar code | Great at "what does this function do" on read-only code you then verify against behavior |
| Boilerplate | Test scaffolds, config files, repetitive CRUD, serialization |
| Draft tests | Good at enumerating cases you then prune and correct |
| Search-term discovery | "What's this pattern called?" unblocks a real search |
| Rubber-ducking | Explaining your problem to it clarifies your own thinking |
| Translating between languages | Idiom-to-idiom, when you can read both |
| Regex / shell incantations | You can test the output immediately |
| First draft of docs | You edit for accuracy; it handles structure |

The common thread: tasks where **verification is cheap** (you can run
it, read it, or test it immediately) or where being wrong is **low
stakes** (a search term, a first draft).

## Where AI Hurts

| Task | Why it fails |
|---|---|
| Load-bearing logic without verification | Confident, plausible, and wrong is the worst combination |
| Multi-file refactors in untyped languages | No compiler to catch the half-applied rename; silent breakage |
| Anything security-sensitive | Auth, crypto, input validation, permissions — subtle errors are catastrophic |
| Anything financial / irreversible | Money, deletes, migrations — the cost of a hallucination is real |
| "What's the current best practice" | Training cutoffs mean stale advice stated confidently |
| Novel algorithms in your specific domain | It pattern-matches to common cases; yours may not be common |
| Architecture decisions | It will happily produce *an* answer with no stake in living with it |

The common thread: tasks where **verification is expensive** or where
**being wrong is costly**.

## Hallucinated APIs Are Real

This is the failure mode to internalize. AI tools confidently invent:

- Functions that don't exist (`os.path.splitall()` — nope).
- Parameters a function doesn't take.
- Library methods from a *different* library.
- Config options that were never implemented.
- Flags that look right but aren't.

It will write `import` statements for packages that don't exist, and
the code will look completely reasonable. There's even a supply-chain
attack class ("slopsquatting") where attackers register the package
names that AIs commonly hallucinate.

**Defense:** check the real docs / source / `--help` for any API you
didn't already know. The LSP helps here — a hallucinated method shows
up red. A compiled, typed language catches most of it. An untyped
language catches none of it until runtime.

## Verification by Stakes

Match your verification effort to the cost of being wrong:

```
Low stakes, cheap to verify    → skim and run it
(boilerplate, a test draft)

High stakes OR expensive verify → read every line, check every API,
(auth, a migration, a refactor)   test the edges, get human review
```

Never let "it looks right" substitute for "I checked." Plausibility is
exactly what these tools optimize for.

## Using AI to Learn a Codebase

A legitimately strong use, with guardrails:

- **Ask it to explain a file or function**, then confirm against
  actual behavior (run it, read the tests).
- **Ask for the *name* of a concept** so you can search for the real
  thing.
- **Ask "where would I look for X"** to generate search hypotheses —
  then use `rg` / LSP to confirm.

What to distrust:

- Claims about *this specific* codebase's behavior it hasn't read.
- "This function is called from..." without it having searched.
- Any architectural claim you can't trace in the code.

Pair AI with the deterministic tools in
[../02-navigation/](../02-navigation/). The AI generates hypotheses;
`rg`, the LSP, and the tests confirm them.

## AI in Code Review

You can ask AI to review your diff before a human does — it catches
typos, obvious bugs, missing error handling. Useful as a *first* pass.

But:
- It misses context-dependent issues (this is fine here because X).
- It flags non-issues confidently (noise you must filter).
- It can't judge whether the change fits the project's direction.

Use it to clean up before human review, not to replace it. And don't
paste AI review comments into someone *else's* PR as if they're your
own judgment — see [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md).

## Disclosure and Etiquette

Norms vary, but safe defaults:

- **Don't dump AI-generated PRs** on maintainers without review. A PR
  full of plausible-but-unverified code wastes their time and burns
  trust fast. Many projects now explicitly reject low-effort AI PRs.
- **Some projects require disclosure** of AI assistance (and the DCO
  sign-off has implications — you're certifying you have the right to
  contribute the code). Check `CONTRIBUTING.md`.
- **Your understanding is the bar.** If a maintainer asks "why did you
  do it this way?" and your answer is "the AI suggested it," you
  weren't ready to open the PR.

## Prompting for Better Results

Tactical notes that improve output quality:

- **Give it the real context.** Paste the actual types, the actual
  error, the actual surrounding code. Vague prompts get vague code.
- **Ask for the approach before the code** on anything non-trivial, so
  you can catch a wrong direction before reviewing 200 lines.
- **Constrain it.** "Use only the standard library," "match the style
  in this file," "no new dependencies."
- **Iterate in small steps** rather than asking for a whole feature at
  once — small diffs are verifiable diffs.

## Anti-Patterns

### Committing code you don't understand

The cardinal sin. If you can't explain each line, you can't maintain
it, debug it, or defend it in review. Understanding is non-negotiable.

### Trusting confidence as correctness

These tools are equally fluent when right and when wrong. Tone carries
zero information about accuracy.

### Skipping the docs because the AI "knows"

Training cutoffs and hallucinations make AI a poor source of truth for
exact APIs. The docs and the source are authoritative; the AI is a
fast-but-fallible assistant.

### Outsourcing judgment

What to build, how to architect it, whether to take a dependency,
whether a tradeoff is worth it — these are yours. The AI has no stake
in the consequences.

### Using it on code you can't verify

If you can't read the language, can't run the code, and can't test the
result, AI output is a liability, not an asset.

## See Also

- [editor-and-lsp.md](editor-and-lsp.md)
- [../02-navigation/search-tools.md](../02-navigation/search-tools.md)
- [../09-unknown-tech/just-enough-learning.md](../09-unknown-tech/just-enough-learning.md)
- [../03-reading-code/tolerance-for-confusion.md](../03-reading-code/tolerance-for-confusion.md)
- [../14-advanced/giving-code-review.md](../14-advanced/giving-code-review.md)
