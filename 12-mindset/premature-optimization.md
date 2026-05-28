# Premature Optimization

"Premature optimization is the root of all evil" is the most
over-quoted and least-understood line in software. The real rule isn't
"never optimize" — it's "don't optimize on a guess." Measure, then act.

## What Knuth Actually Meant

The full quote: *"We should forget about small efficiencies, say about
97% of the time: premature optimization is the root of all evil. Yet we
should not pass up our opportunities in that critical 3%."*

The point was never "performance doesn't matter." It was:

- Most code isn't hot, so optimizing it buys nothing but complexity.
- You usually **can't tell which 3% is hot by guessing** — you have to
  measure.
- The 3% that *is* hot deserves real, deliberate optimization.

So the rule is about *sequencing and evidence*, not abstinence.

## Correct First, Fast Second

Write the simple, obviously-correct version first:

- It's the baseline you'll measure against.
- It's the fallback if the optimization proves not worth it.
- It's often *fast enough*, and you just saved yourself the complex
  version entirely.

Clarity is the default; speed is earned with evidence. A correct, clear
implementation that's "too slow" is a great problem to have — you can
profile it and fix the real hot spot. A fast, clever implementation
that's subtly wrong is a much worse one.

## Measure First — Always

Before optimizing anything, profile it. Every experienced engineer has
a story where the bottleneck was nowhere near where they "knew" it was.
Intuition about performance is famously unreliable because:

- The hot path is often a surprise (a logging call, a serialization, an
  N+1 query — not your fancy algorithm).
- Modern hardware (caches, branch prediction, JITs) makes naive
  reasoning wrong.
- The slow part is frequently I/O or allocation, not computation.

The tools are in
[../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md) and
the discipline in
[../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md).
Reach for them *before* you change anything in the name of speed.

## Benchmark Numbers or No Merge

The standard for a performance PR:

> If you claim it's faster, show the numbers.

A PR that says "optimized the parser" with no measurement is unmergeable
on principle — not because it's necessarily wrong, but because nobody,
including you, knows if it helped. Include:

- **Before and after** numbers, from the same machine, same input.
- **The benchmark itself**, so it's reproducible and can guard against
  regressions later.
- **Representative input** — production-like size and shape, not a toy.

```
benchmark         old           new           delta
ParseLargeFile    1240ms        310ms         -75%
ParseSmallFile    0.9ms         0.9ms         ~
```

`hyperfine` for CLIs, language-native benchmark harnesses for code (Go's
`testing.B`, Rust's `criterion`, `pytest-benchmark`, JMH for the JVM).

## Keep the Simple Version Available

When you do optimize, don't delete the clear version from history or
mind. Options:

- Leave the naive implementation as the reference in a comment or a
  test oracle (the optimized version must match its output).
- Keep a `// simple but slower: ...` note explaining what the complex
  code replaced and why.
- Property-test the fast path against the slow path.

This protects against the classic failure: a clever optimization that's
subtly wrong, with no simple version left to check it against.

## When Optimization *Is* Warranted Up Front

The "don't optimize early" advice has real exceptions — places where the
cost is baked in and expensive to change later:

- **Algorithmic complexity.** Choosing O(n²) where O(n log n) is
  natural isn't "premature optimization" — it's a design decision.
  Picking the right data structure up front is just competence.
- **Hot paths you already know.** If you've profiled this system before
  and know the inner loop, design for it.
- **Architectural choices.** Pagination, batching, indexing,
  caching boundaries — retrofitting these is far costlier than
  designing them in.

The distinction: optimizing *structure and algorithms* up front is
prudent; micro-optimizing *implementation details* up front is
premature. Don't hand-unroll a loop before you know it's hot; do pick
the right complexity class from the start.

## The Cost Side of the Ledger

Every optimization has a price, paid by every future reader:

- More complex code → harder to read (you pay the 10x from
  [reading-vs-writing.md](reading-vs-writing.md)).
- More edge cases → more bugs.
- Harder to change → slower future work.

An optimization is only worth it when the speed gain *exceeds* this
ongoing complexity tax. A 2% speedup on cold code that doubles the
reading difficulty is a net loss.

## Anti-Patterns

### Optimizing without profiling

The cardinal sin. You'll spend effort speeding up code that wasn't slow
and miss the part that was. Profile first, every time.

### "It might be slow someday"

Speculative optimization for load you don't have, with code you'll
maintain forever. Build for today's requirements; optimize when the
profiler (or a real SLO) says to.

### Micro-optimizing cold paths

Hand-tuning code that runs once at startup buys nothing and costs
clarity. Spend the effort where the time actually goes.

### Merging perf claims without numbers

"Should be faster" is a hypothesis, not a result. No benchmark, no
merge.

### Throwing away the simple version

Once the clever version is the only version, you've lost your
correctness oracle. Keep the simple one around to check against.

## See Also

- [reading-vs-writing.md](reading-vs-writing.md)
- [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)
- [../10-features-refactors/scope-discipline.md](../10-features-refactors/scope-discipline.md)
