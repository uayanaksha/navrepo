# Performance Investigation

Performance work done by intuition is mostly wasted motion. Done by
measurement, it's some of the highest-leverage work there is. The
discipline is simple to state and hard to follow: measure, find the real
bottleneck, fix *that*, measure again.

## The Iron Law: Measure First

You cannot optimize what you haven't measured, because **your intuition
about where the time goes is almost always wrong.** Every experienced
engineer has a story of spending a day optimizing the function they
"knew" was slow, only to find the real cost was an accidental N+1 query,
a logging call, or a serialization step nowhere near their guess.

So before changing a single line for performance:

1. **Reproduce** the slowness with a representative workload.
2. **Measure** to find where the time actually goes (profile).
3. **Fix** the biggest real bottleneck.
4. **Measure again** to confirm it helped and find the *new*
   bottleneck.

The mindset rationale is in
[../12-mindset/premature-optimization.md](../12-mindset/premature-optimization.md);
the tools are in
[debugging-toolkit-deep-dive.md](debugging-toolkit-deep-dive.md). This
page is the method that connects them.

## Define "Fast Enough" First

Optimization without a target is endless. Before you start, know:

- **The goal.** "p99 latency under 200ms," "process 1M rows in under a
  minute," "render in one frame (16ms)." A number, not "faster."
- **The workload that matters.** Optimizing for input you don't have is
  waste. Use production-representative data — size, shape, distribution.
- **When to stop.** When you hit the target. Past that, you're spending
  complexity for speed nobody needs (see the cost ledger in
  [../12-mindset/premature-optimization.md](../12-mindset/premature-optimization.md)).

## Find the Bottleneck

### Profile, don't guess

Use the right profiler for the symptom (see
[debugging-toolkit-deep-dive.md](debugging-toolkit-deep-dive.md)):

- **CPU-bound** (CPU pegged) → on-CPU profiler + flame graph; find the
  widest frames.
- **Slow but CPU idle** → off-CPU / blocking analysis; the time is in
  waiting (I/O, locks, network).
- **Memory pressure / GC** → allocation profiler.

### The 5% / 95% rule

A classic distribution: roughly **5% of the code accounts for 95% of the
runtime.** Almost all your gains come from finding and fixing that 5%.
Optimizing the other 95% is effort spent for nearly nothing — and it
adds complexity to code that wasn't slow.

This is why measurement dominates: the entire game is *locating the 5%*.
Once located, the fix is often easy and obvious.

### Amdahl's Law (why the hot path is everything)

If a part of the program takes 80% of the time and you make it
infinitely fast, you get at most a 5x speedup — the other 20% now
dominates. Corollary: **optimizing anything that isn't a large fraction
of total time can't help much, no matter how clever.** Always attack the
biggest slice first.

## Common Bottleneck Categories

Where the time usually actually goes, in rough order of frequency:

| Category | Typical culprit | Typical fix |
|---|---|---|
| **I/O / queries** | N+1 queries, missing index, chatty network | Batch, index, cache, fewer round-trips |
| **Algorithmic** | Accidental O(n²), repeated work | Better algorithm/data structure |
| **Allocation/GC** | Churn in a hot loop | Reuse buffers, reduce allocations |
| **Serialization** | JSON encode/decode on the hot path | Cheaper format, do it less |
| **Locking** | Contention, serialized critical section | Reduce lock scope, lock-free, shard |
| **Cache effects** | Poor locality, cache misses | Data layout, batching |

Notice how few of these are "the math is slow." The bottleneck is rarely
raw computation — it's usually I/O, waiting, or doing work you didn't
need to do at all.

## The Cheapest Optimization: Do Less

Before making code *faster*, ask if you can make it *do less*:

- **Cache** a result instead of recomputing it.
- **Batch** N round-trips into one.
- **Lazy/defer** work that isn't needed yet.
- **Precompute** what's reused.
- **Eliminate** the work entirely (the fastest code is no code).

These often beat micro-optimizing the work itself by orders of
magnitude, with *less* complexity, not more.

## Benchmark Properly

Numbers only mean something if measured well:

- **Warm up.** JITs, caches, and connection pools make the first runs
  unrepresentative. Discard warmup; measure steady state.
- **Repeat and report variance.** A single run is noise. Report
  mean *and* spread (`hyperfine` does this for CLIs; use the language's
  benchmark harness for code — Go `testing.B`, Rust `criterion`,
  `pytest-benchmark`, JMH).
- **Control the environment.** Background load, thermal throttling, and
  noisy neighbors skew results. Compare on the same machine, same
  conditions.
- **Measure the right thing.** Microbenchmarks lie when the real cost is
  systemic (a microbench can't see GC pressure or cache effects across
  the whole program). Validate with an end-to-end measurement.
- **Keep the benchmark.** Commit it so it guards against regressions and
  documents the win. No before/after numbers, no merge (see
  [../12-mindset/premature-optimization.md](../12-mindset/premature-optimization.md)).

## Verify and Stop

After a change:

- **Confirm correctness.** A fast wrong answer is worthless — keep the
  simple version as an oracle (see legacy-code's characterization tests,
  [working-with-legacy-code.md](working-with-legacy-code.md)).
- **Confirm the gain** with the same benchmark. If it didn't move the
  number, revert it — you added complexity for nothing.
- **Re-profile.** The bottleneck moved; the new one may be elsewhere
  entirely. Stop when you hit the target.

## Anti-Patterns

### Optimizing by intuition

Changing the code you "know" is slow without profiling. It's almost
always the wrong code. Measure first.

### No baseline, no target

Optimizing with no "before" number and no goal — you can't tell if you
helped or when to stop. Define both up front.

### Micro-optimizing outside the hot 5%

Hand-tuning code that's a rounding error in the total. Amdahl's law says
it can't matter. Attack the biggest slice.

### Trusting a microbenchmark

A microbench shows a 10x win; the end-to-end system is unchanged because
the function wasn't the bottleneck. Validate end-to-end.

### Sacrificing correctness for speed

A faster version that's subtly wrong. Always verify against the known-
correct (often simpler) implementation.

### Optimizing prematurely

Complex performance code written before there's evidence it's needed,
paid for in readability forever. Wait for the profiler (or a real SLO)
to say so.

## See Also

- [../12-mindset/premature-optimization.md](../12-mindset/premature-optimization.md)
- [debugging-toolkit-deep-dive.md](debugging-toolkit-deep-dive.md)
- [working-with-legacy-code.md](working-with-legacy-code.md)
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)
- [working-in-distributed-systems.md](working-in-distributed-systems.md)
