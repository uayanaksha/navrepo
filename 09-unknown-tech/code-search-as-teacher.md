# Code Search as Teacher

When docs are incomplete or you're not sure what's idiomatic, the
fastest way to learn is to find existing examples of what you're
trying to do and read them.

## Find Patterns in the Wild

For any "how do I do X?":

```bash
# Find handlers in this project
rg 'async fn.*Response' --type rust

# Find places using ORM filter
rg '\.filter\(' --type python

# Find similar tests
rg 'test_payment_' --type python tests/
```

Read 3–5 examples. Patterns emerge.

This is faster than reading documentation, and more authoritative —
the examples are what actually works in this codebase.

## Within the Project

The closest patterns are in the project you're working in:

- "How do they handle errors?" → `rg 'return Err'` or `rg 'raise '`.
- "How do they structure handlers?" → look at the handler folder.
- "How do they write tests?" → look at the test folder.

This matches the project's conventions, which is what you want.

## Across the Ecosystem

For language / framework patterns:

### GitHub code search

```
language:rust .await
language:python "@app.get"
language:typescript useState
```

[github.com/search?type=code](https://github.com/search?type=code)

### Sourcegraph

For deeper queries; can find references across orgs. Free for public
code.

### grep.app

Fast, simple cross-public-repo grep.

### Stack Overflow

For specific questions, often has accepted answers with explanations.
Trust the recent, upvoted answers.

## Reading Open-Source Reference Projects

Some projects are particularly worth reading:

| Language | Reference projects |
|---|---|
| Rust | `tokio`, `axum`, `clap`, `serde` |
| Go | Standard library (`net/http`), `kubernetes`, `docker` |
| Python | `requests`, `flask`, `pytest` |
| TypeScript | `vscode`, `nestjs`, `prisma` |
| Java | Spring framework |

These have idiomatic, well-reviewed code. Reading 30 minutes of any of
them teaches a lot.

## "How Do I X?" Workflow

For an unfamiliar operation:

1. Search the project for similar usage:
   ```bash
   rg '<keyword>' --type <lang>
   ```
2. If found, read 2–3 examples.
3. If not, search the framework's docs.
4. If still not, search GitHub broadly.
5. If still not, ask in the project's chat.

Skip steps as needed. Most tasks resolve at step 1 or 2.

## When You Find Inconsistent Examples

Sometimes the project has multiple patterns for the same thing:

```python
# style A
try:
    ...
except Exception as e:
    log.error(...)

# style B
result = safe_operation()
if not result.ok:
    log.error(...)
```

Both exist. Which to use?

- **Pick the one used by the *recent* code.** Git log tells you.
- **Pick the one used in *adjacent* files.** Match the area.
- **Ask** if you're not sure.

Don't introduce a third style.

## Pattern vs Anti-Pattern

Not every pattern in the wild is good:

- Some patterns are legacy ("we used to do it this way; new code does
  X").
- Some patterns are workarounds for old bugs.
- Some patterns are personal style of one contributor.

Calibrate by:

- **Author**: did a respected maintainer write this?
- **Date**: is it recent?
- **Frequency**: do many files do it this way?
- **Convention**: does the style guide endorse this?

If unsure, follow the most-recent example by the most-active
contributor.

## Patterns That Don't Transfer

A pattern from one project may not apply to yours:

- The project may use a different framework version.
- The project may have different scale (concurrency, data size).
- The project may have specific constraints (no async, no allocator).

Don't blindly copy from a different project. Verify the constraints
match.

## When You Can't Find Examples

For genuinely novel things:

- Build the smallest version that compiles.
- Test.
- Refine.

Sometimes you're at the edge of the ecosystem. That's OK; you'll
write the first example.

## Counter-Anti-Pattern: "I Found a Better Way"

Sometimes you find an outside pattern that seems better than the
project's. Tempting to switch.

Resist. Especially for early contributions. The project's style is
self-consistent; randomly introducing a "better" pattern fragments
that.

Wait until you've done many PRs and earned trust. Then propose the
change as a discussion, not a unilateral PR.

## Patterns at Different Levels

Code search can answer questions at multiple levels:

### Syntax level

"How do I declare an async function?" → `rg 'async fn'`.

### API level

"How does this library's HTTP client work?" → search for actual usage.

### Architectural level

"How do projects structure their microservices?" → read several
exemplary OSS services.

Match your search to the question's level.

## Using AI to Find Patterns

AI tools can search for patterns and explain them. Useful for:

- "Show me an example of X in language Y."
- "What does this code idiom mean?"

But: AI hallucinates plausibly. Verify any code it produces by
finding real-world examples.

## The "Read 10 Examples" Habit

Before writing a non-trivial piece of code, read 10 examples of
similar work. Yes, 10.

The first 3 give you a baseline.
The next 3 reveal variation.
The next 4 calibrate what's normal.

After 10, you have informed judgment, not guessing.

This takes 30 minutes. Saves hours of refactoring later.

## See Also

- [../02-navigation/search-tools.md](../02-navigation/search-tools.md)
- [../03-reading-code/pattern-recognition.md](../03-reading-code/pattern-recognition.md)
- [cargo-cult.md](cargo-cult.md) — don't blindly copy
