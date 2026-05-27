# Language vs Codebase Confusion

When stuck in unfamiliar tech, classify your confusion. Different
confusions need different responses.

## The Five Types

### 1. Language confusion

"I don't understand this syntax / construct."

Examples:
- "What does `'static` mean in Rust?"
- "What's `yield` doing in this Python function?"
- "Why does this Go function start with `func (s *Server)`?"

Fix: language docs, tutorial chapter, AI explanation. **Bounded** —
the language is finite.

### 2. Framework / library confusion

"I don't understand what this library is doing."

Examples:
- "What does `useEffect` actually do?"
- "Why is this `Provider` necessary?"
- "What's a `Cargo.toml` workspace?"

Fix: library docs, framework guide, examples. Often the library has
canonical learning material.

### 3. Codebase convention confusion

"I don't understand why this project does X this way."

Examples:
- "Why does this project wrap all errors with `anyhow::Context`?"
- "Why is there a `pkg/` and an `internal/` and what's the difference
  in this project?"
- "Why does every service have a `Manager` class?"

Fix: project docs (ARCHITECTURE.md, ADRs), existing examples, ask a
maintainer.

### 4. Domain confusion

"I don't know what an X is in real life."

Examples:
- "What's an FX swap?"
- "What does GAAP-compliant mean?"
- "What's an L1 vs L2 protocol?"

Fix: domain docs, glossary, subject-matter expert. Sometimes outside
the project's docs entirely.

### 5. Genuine ambiguity / bug

"The code itself is wrong or under-specified."

Fix: investigate, then propose change or ask.

## Diagnosis Questions

When stuck, ask:

1. **Could I look this up in a language reference?** → language
   confusion.
2. **Could I look this up in the library's docs?** → library
   confusion.
3. **Is this specific to *this* project?** → codebase confusion.
4. **Would I be confused even if someone explained the code line by
   line?** → domain confusion.
5. **Is the code itself unclear / unclear / wrong?** → ambiguity / bug.

Often confusion is **multiple types layered**. Untangle them.

## Why the Distinction Matters

Each type has a different cost:

| Type | Resolution time |
|---|---|
| Language | Hours to days (one-time investment) |
| Framework | Hours to days (per framework) |
| Codebase | Days to weeks (per codebase) |
| Domain | Weeks to months (per domain) |
| Ambiguity | Variable, requires conversation |

Recognizing you're in codebase-confusion (not language confusion) tells
you to ask a maintainer, not read a tutorial.

## Mistaking Codebase for Language

A common error: "I don't understand Rust" when actually "I don't
understand this Rust project's specific patterns."

Examples:

```rust
pub fn handle<T: Handler>(t: T, ctx: Context) -> Result<Response, Error>
```

Generic shape, but each project may have its own `Handler`, `Context`,
`Response`, `Error`. The language is fine. The project's vocabulary is
what you're learning.

Tell when:

- Existing project examples make it click.
- Outside reference material doesn't directly apply.
- A maintainer can explain in two sentences.

## Mistaking Domain for Code

Symmetric error: "this code is confusing" when actually "this domain
is unfamiliar."

```python
def calculate_holdback(invoice: Invoice) -> Decimal:
    if invoice.holdback_pct is None:
        return Decimal(0)
    return invoice.amount * invoice.holdback_pct / 100
```

The code is straightforward. But what's a "holdback"? Domain
knowledge.

Tell when:

- The code reads cleanly but the *purpose* is unclear.
- The variable names look like words you don't fully grasp.
- You'd need a domain glossary to fully understand.

## Mistaking Ambiguity for Confusion

Sometimes the code is genuinely unclear. Symptoms:

- Multiple maintainers can't explain it briefly.
- `git blame` shows commits with messages like "I think this works."
- Tests don't fully cover the case.
- Comments contradict the code.

Don't blame yourself. The code is the problem. Options:

- Add a clarifying comment (as part of a PR).
- Add a test that pins behavior.
- Open an issue describing the ambiguity.

## When You Don't Know Which

If you can't classify, the catch-all moves work:

- **Read more code.** Often patterns emerge.
- **Ask a maintainer** — they can tell you which type ("you'd find
  this in the X docs" vs "yeah, project-specific weirdness").
- **Build a toy** to test your understanding ([build-a-toy.md](build-a-toy.md)).

## Avoiding Conflation in Your Own Communication

When you describe your confusion to others, specify the type:

Bad: "I don't understand this code."
Good: "I understand the language; I'm not sure what this project's
`Manager` pattern is meant to enforce."

The maintainer's response will be much more targeted.

## Confusion Hierarchies in Practice

A real example:

You join a Kubernetes operator project written in Go.

1. **Language**: You know Go basics; minimal confusion.
2. **Framework**: You don't know controller-runtime; need to learn.
3. **Codebase**: This project has a custom `Reconciler` shape;
   project-specific.
4. **Domain**: What are CRDs, operators, custom resources?

Two days to bridge framework + codebase. Domain knowledge layers on
top.

Don't think "I'm slow." Multiple confusions stack.

## Resolving Faster

Speed-ups:

- **Ask early.** Maintainers can save you days.
- **Read tests first.** Tests bridge codebase + framework.
- **Write tiny code.** Try the pattern; see what happens.
- **Pair if possible.** 1 hour of pairing beats 8 hours alone.

## See Also

- [just-enough-learning.md](just-enough-learning.md)
- [../03-reading-code/tolerance-for-confusion.md](../03-reading-code/tolerance-for-confusion.md)
- [code-search-as-teacher.md](code-search-as-teacher.md)
