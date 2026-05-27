# Just-Enough Learning

When you need to work in a language or framework you don't know, the
mistake is trying to "learn" it before doing the task. You can't.
Learning happens *through* the work.

## The Principle

Aim for **the smallest amount you need** to make this specific change.
Treat anything beyond as deferred.

If your task is "fix a typo in a Rust function," you don't need to
learn ownership and lifetimes. You need to find the function, read
five lines, and edit.

If your task is "add a new HTTP route in this Go service," you need:
- How HTTP routing works in this framework.
- Roughly what Go's structure / packages look like.
- How to run tests.
- Not much else.

The list of "things you don't yet need" stays long. That's fine.

## The Sequence

A reliable order:

### 1. Official "getting started"

30 minutes max. Just enough to:
- Run a hello-world.
- Know how to install / build / test.
- Recognize the language / framework's vocabulary.

Don't take notes; don't memorize syntax. Just absorb shape.

### 2. The standard library / core API

Skim — don't read. Notice what's there. You'll look up specifics later.

For a language: standard collections, IO, error handling.
For a framework: routing, middleware, the main "abstraction."

### 3. One real example

Find existing code in the repo (or in widely-used OSS) that uses what
you need:

```bash
# Want to learn how to write a Rust async handler:
rg 'async fn.*-> Response' --type rust path/to/repo
```

Read 2–3 examples. Patterns emerge.

### 4. Build something tiny

A 50-line throwaway that exercises the bit you need. Doesn't have to
be good. Doesn't have to be saved. The exercise is the value.

### 5. Return to your task

You now have:
- Enough vocabulary to read code.
- Enough syntax to make changes.
- Enough mental model to know when you're wrong.

That's enough.

## What Not to Do

### Don't "learn the language" first

You can't pre-load a language. Books and courses give you words; the
language gets internalized through doing.

Most "learn $LANG" courses take 20–40 hours. That's months of
calendar time. Your task doesn't need months.

### Don't memorize syntax

You'll look up syntax for years. That's fine. Memorizing slows you
down without long-term benefit.

### Don't aim for idiomatic on first try

Your code in a new language will be ugly. That's normal. You'll
learn idiom by reading examples and getting feedback.

Don't paralyze yourself on "what's the right way" for ten minutes.
Write *a* way. Improve later.

## The "Trust" Move

Many things in a new language do what you expect. Trust the names.

```rust
let result = some_iterator.filter(|x| x.active).map(|x| x.id).collect();
```

You don't know Rust. But you know `filter`, `map`, `collect`. The
shape is recognizable. Even if you don't understand `|x|` syntax,
you can guess. Move forward.

When the guess fails, you'll learn what `|x|` is. Now you know.

## Reading vs Writing in Unknown Tech

You can **read** much further than you can **write** in a new
language. This is normal and OK.

Strategy: read aggressively (with LSP help, hover for types). Write
minimally. Copy patterns from existing code.

After a few weeks, your writing catches up.

## The Tutorial Trap

Tutorials feel productive. They mostly aren't.

What they give: completion, momentum, a sense of progress.
What they don't give: judgment, context, the *why*.

Tutorials are useful for:
- The first 30 minutes (orientation).
- A specific concept you're stuck on ("how does async/await work in
  Python?").

They're not useful for:
- Becoming "ready" to contribute.
- Understanding the project you're working in.

After 30 minutes of tutorial, switch to real code.

## Documentation Reading

A modern docs site for a library is often 200+ pages. Don't read it
all.

Strategy:

1. Skim the **landing page** to know what exists.
2. Skim the **table of contents** to know the structure.
3. Read the **quickstart** if you haven't.
4. Bookmark the **API reference** for lookups.
5. Read **specific pages** when you need them.

You'll touch maybe 5% of the docs.

## When the Docs Are Bad

Some libraries have terrible docs. Workarounds:

- **Read the source.** Most libraries are smaller than their docs
  suggest.
- **Read tests.** They show intended use.
- **Read examples** if the repo has them.
- **Search for blog posts.** Often someone wrote the missing tutorial.

## Asking for Help

When stuck after 30 minutes:

- Ask in the project's chat ("how do I do X in this stack?").
- Ask in the language's chat (Rust Discord, Python channel, etc.).
- StackOverflow / forums.

The threshold for asking is lower in a new tech than in your
specialty. People know you're learning; they're often happy to point.

## Calibrating Your Confidence

In a new tech:

- "I'm 80% sure this works" → run the tests; verify.
- "I'm 50% sure" → ask before shipping.
- "I have no idea" → ask for review before merging.

Don't fake confidence. People can tell, and it produces bad code.

## When the Tech Itself Doesn't Fit

Sometimes after exploration you realize: this tech isn't the right
tool. The project chose poorly, or the use case has outgrown it.

Mention in the PR if relevant ("worth considering moving to Y for
this kind of work — happy to discuss separately"). But don't make
the bugfix a "we should rewrite" PR.

## Building Vocabulary Over Time

Each new tech leaves you with:

- New keywords.
- New idioms.
- New mental models.

These accumulate. The 10th language is faster than the 2nd because
many concepts transfer.

Notice patterns across languages:
- Most have iteration.
- Most have error handling (exception, Result, or both).
- Most have async / concurrency primitives.
- Most have package management.

The shape recurs.

## See Also

- [lsp-as-tutor.md](lsp-as-tutor.md)
- [code-search-as-teacher.md](code-search-as-teacher.md)
- [build-a-toy.md](build-a-toy.md)
- [../15-language-ecosystems/](../15-language-ecosystems/)
