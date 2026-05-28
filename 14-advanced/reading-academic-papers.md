# Reading Academic Papers

Behind many systems you use — databases, consensus protocols,
schedulers, data structures — is a paper. Being able to read it lets you
understand the *why* at the deepest level and implement ideas before
they're popularized. Papers are dense by convention, not necessity;
there's a technique.

## Why Bother

- **The source of truth.** Blog posts summarize papers, often
  inaccurately. The paper is the actual design and its guarantees.
- **Implement before it's a library.** Reading the paper lets you build
  the idea when no off-the-shelf version exists yet.
- **Understand your tools deeply.** Knowing the Raft paper makes every
  consensus system make sense; knowing the LSM-tree paper explains a
  whole class of databases.
- **Calibrated skepticism.** Papers have assumptions and limitations
  that the hype omits. Reading them tells you when the idea *doesn't*
  apply.

## The Three-Pass Method

The standard technique (from S. Keshav's "How to Read a Paper") — read a
paper in up to three passes, each deeper, bailing out when you've
learned enough.

### Pass 1 — The bird's-eye view (5–10 min)

Read only: **title, abstract, introduction, section headings,
conclusions**, and skim the references. Goal: answer the "five Cs" —

- **Category:** what kind of paper is it?
- **Context:** what other work does it relate to?
- **Correctness:** do the assumptions seem valid?
- **Contributions:** what's the main claim?
- **Clarity:** is it well-written?

After pass 1, decide: do I need more? Often this is enough — you know
what the paper claims and whether it's relevant. Most papers you
encounter, you should *stop here*.

### Pass 2 — The content (~1 hour)

Read the paper with more care, but **ignore the proofs and heavy math**.
Focus on:

- Figures, diagrams, and graphs — are the axes labeled, the results
  significant?
- The core mechanism — how does the thing actually work?
- Mark terms you don't know and references to read later.

After pass 2 you should be able to *summarize the paper's main idea to
someone else*. For most papers you'll read as an engineer, this is the
stopping point.

### Pass 3 — The deep dive (several hours)

Only for papers you must deeply understand or implement. **Virtually
re-create the work**: re-derive the key results, challenge every
assumption, think about how *you* would have done it. This is where you
find hidden assumptions and unstated limitations — and where you're
ready to implement.

## Separate Contribution from Background

A crucial reading skill: papers spend pages on **background** (what
others did, foundational setup) before the **contribution** (what's
actually new here). Don't drown in the background thinking it's the
point.

- Ask continuously: **"What is *new* in this paper?"** That's usually a
  small, specific core idea wrapped in a lot of context.
- The contribution is often one or two key insights or mechanisms. Find
  them; the rest is scaffolding to explain and justify them.
- The related-work section is background — useful for context and
  further reading, but not the thing to understand first.

## Implement a Toy Version

The deepest understanding comes from building it. After pass 3, write a
*minimal, toy* implementation:

- Strip away the production concerns (scale, edge cases, optimizations)
  and implement the *core mechanism* only.
- A toy Raft, a toy LSM-tree, a toy ring buffer — small enough to finish,
  complete enough to prove you understood.
- The places where your toy *breaks* or where you get *stuck* are
  exactly the subtleties the paper was addressing. That's the learning.

This mirrors the "build a toy" learning strategy for any unfamiliar tech
(see [../09-unknown-tech/build-a-toy.md](../09-unknown-tech/build-a-toy.md)).

## Practical Tactics

- **Read the related work last,** not first — you'll appreciate it once
  you know the contribution.
- **Look up the talk.** Many papers have an accompanying conference talk
  by the authors that's far more digestible (see
  [../13-hidden-knowledge/conference-talks.md](../13-hidden-knowledge/conference-talks.md)).
- **Find a reference implementation** to read alongside — seeing the idea
  in code grounds the abstraction.
- **Don't get stuck on notation.** If the math notation blocks you, push
  past it on pass 2; the *idea* usually survives without following every
  symbol.
- **Read with a question.** "How does X actually achieve Y?" keeps you
  oriented through the density.
- **Discuss it.** Explaining a paper to someone (or writing a summary)
  exposes what you didn't actually understand.

## Calibrate Your Skepticism

Papers are arguments, not gospel:

- **Check the assumptions.** A result valid under "no failures" or "small
  N" may not hold in your reality.
- **Note what's evaluated.** Impressive benchmarks on a workload unlike
  yours may not transfer.
- **Beware unreproduced claims.** A single paper's result, especially
  without independent replication, is a hypothesis. (The replication
  crisis isn't only in other fields.)
- **Mind the date.** An old paper may be superseded; a new one may be
  unvalidated. Both are useful, differently.

## Anti-Patterns

### Reading linearly, start to finish

Slogging through every section in order, drowning in background and math
before reaching the point. Use the three passes; bail when you've
learned enough.

### Mistaking background for contribution

Spending your effort understanding the related-work setup instead of the
new idea. Always hunt for "what's new here."

### Getting stuck on the math

Abandoning a paper because pass-2 proofs are dense. Skip the proofs; the
core idea usually survives without them.

### Treating papers as gospel

Implementing a paper's idea without checking whether its assumptions
hold in your context. Calibrate skepticism; check the limitations.

### Understanding without building

Believing you "get" a complex mechanism you've never implemented. The
toy implementation reveals everything you glossed over.

## See Also

- [../09-unknown-tech/build-a-toy.md](../09-unknown-tech/build-a-toy.md)
- [../13-hidden-knowledge/conference-talks.md](../13-hidden-knowledge/conference-talks.md)
- [../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md)
- [working-in-distributed-systems.md](working-in-distributed-systems.md)
- [../03-reading-code/tolerance-for-confusion.md](../03-reading-code/tolerance-for-confusion.md)
