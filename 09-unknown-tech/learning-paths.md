# Learning Paths

Suggested sequences for picking up unfamiliar tech. Not exhaustive —
adjust to your context.

## The Generic 1-Day Path

Any new language or framework:

| Hour | Activity |
|---|---|
| 1 | Run hello-world, get LSP/tools working |
| 2 | Read getting-started doc; build a tiny app |
| 3 | Read 3–5 examples in the actual codebase |
| 4 | Try your task; ask LSP and existing code |
| 5–6 | Build / debug your task with help |
| 7 | Self-review; ask for human review |

By end of day, you have a working PR. You're not fluent, but you've
shipped.

## Specific Sequences

### Picking up a new language

Order:

1. **Install tools** (compiler, package manager, LSP).
2. **Hello world** (verify toolchain).
3. **Read syntax overview** (1–2 hours; just notation).
4. **Stdlib tour** (skim, not memorize).
5. **Error handling** (often the most-distinct concept).
6. **Build something tiny** (~100 lines, exercising main features).
7. **Read code from a popular project in this language.**
8. **Try your real task.**

### Picking up a new framework

1. **Quick start** from official docs.
2. **The "main abstraction"** — what's the unit (Component, Handler,
   Job)?
3. **Routing / dispatch** — how does input get to your code?
4. **Configuration** — how is it shaped?
5. **Testing** — how do you test in this framework?
6. **One real example** — from the project you'll work in.
7. **Try your task.**

### Picking up an unfamiliar build system

1. **The build manifest** — `pom.xml`, `BUILD.bazel`, `Cargo.toml`.
2. **The basic commands** — build, test, lint.
3. **How to add a new module / target.**
4. **How dependencies are declared.**
5. **CI integration** — how does CI run the build?

You may not need full understanding. Often "run these commands; don't
touch the manifest" is enough.

### Picking up a database

1. **What kind?** Relational, document, graph, time-series, KV.
2. **Schema language** — DDL, modeling, migrations.
3. **Query language** — SQL, MQL, Cypher, etc.
4. **Connection pattern** — how does your code talk to it?
5. **Transactions** — what guarantees does this DB offer?
6. **Performance considerations** — indexes, partitioning, vacuum.

Pair with the project's existing usage; don't try to learn the whole
DB up front.

### Picking up a frontend framework

1. **The mental model** — components, state, rendering.
2. **The tooling** — bundler, dev server, hot reload.
3. **Routing.**
4. **State management** — local state, global state, server state.
5. **Styling approach** — CSS modules, utility-first, CSS-in-JS.
6. **Testing.**

Frontend frameworks have a lot of moving parts. Don't try to absorb
all at once.

### Picking up Kubernetes / cloud

1. **The control loop** — declarative model.
2. **Pods, Deployments, Services** — the basic resources.
3. **kubectl** basic commands.
4. **One real manifest** from the project.
5. **Debugging** — `kubectl describe`, `logs`, `events`.

Beyond this, learn what your task needs (StatefulSets, Operators,
Helm, etc.).

### Picking up a paradigm shift

For really new paradigms:

| From → To | Key shift |
|---|---|
| OOP → Functional | Immutability, pure functions, monads |
| Imperative → Reactive | Streams, observables, declarative pipelines |
| Sync → Async | Concurrency, suspension, race conditions |
| Static → Dynamic typing | Tests as types, runtime debugging |
| Monolith → Distributed | Failure modes, eventual consistency |

Paradigm shifts take longer. Read foundational material *and* build a
toy.

## "I Have N Hours" Plans

### 1 hour

Just orient. Install tools. Run hello-world. Open the project. Read
3 files.

You won't ship today. You'll have a sense of the landscape.

### 1 day

Generic 1-day path above. Ship something small.

### 1 week

Build deeper:

- Day 1: orientation + small task.
- Day 2–3: medium tasks; read related code.
- Day 4: build a toy of a confusing concept.
- Day 5: tackle the harder task with the foundation in place.

### 1 month

Now you can build real things:

- Week 1: orientation + small contributions.
- Week 2–3: medium features; review others' code.
- Week 4: substantive feature or refactor.

By end of month, you're a productive contributor.

### 1 year

Now you're fluent (in this stack, not in everything). Specialization
sets in:

- Months 1–3: broad fluency.
- Months 4–6: deep work in a few areas.
- Months 7–12: own significant pieces of the codebase.

## Learning Curve Realism

You will be slow at first. This is normal.

| Time in tech | Productivity vs your specialty |
|---|---|
| Day 1 | ~10% |
| Week 1 | ~30% |
| Month 1 | ~60% |
| Month 3 | ~80% |
| Year 1 | ~95% |
| Year 3 | parity (or you've specialized further) |

Estimates vary wildly by tech and individual.

## When to Specialize

After ~1 year in a stack, you can:

- Stay broad (many techs, generalist).
- Go deep (master one stack).

Both valid careers. Broad serves you for varied roles; deep serves
you for systems work or open-source maintenance.

Most engineers do both: broad fluency + 1–3 deep specialties.

## Forgetting

You'll forget tech you don't use:

- After 6 months: rusty.
- After 1 year: significantly forgotten.
- After 3 years: starting over (somewhat).

This is fine. Re-learning is faster than first-learning.

Keep notes from your first learning ([../03-reading-code/note-taking.md](../03-reading-code/note-taking.md)) — they accelerate re-learning.

## Resources

For each tech, find:

- **Official docs** — first stop.
- **One book** — for depth (if it exists).
- **One blog** — by a respected practitioner.
- **One YouTube channel / conference talk series** — for visual
  learning.
- **Community chat** — for live help.

Five sources, not 50. Quality beats quantity.

## Anti-Patterns

### Course addiction

Endless tutorials, no real work. Tutorials are a starting point; not
a substitute for shipping.

### "I'll be ready when..."

You'll never feel ready. Start before you're ready.

### Overlearning the language

Knowing every Rust trait isn't useful if you can't make a small PR
in a Rust project. Practical > exhaustive.

### Underlearning the framework

You know the language but not the framework — you copy patterns
without understanding. Spend time on the framework specifically.

## See Also

- [just-enough-learning.md](just-enough-learning.md)
- [build-a-toy.md](build-a-toy.md)
- [../15-language-ecosystems/](../15-language-ecosystems/) — per-stack specifics
