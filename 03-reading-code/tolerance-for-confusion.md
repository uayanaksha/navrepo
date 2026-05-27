# Tolerance for Confusion

The actual skill of code reading isn't comprehension. It's **staying
productive while you don't yet comprehend**.

Beginners panic at confusion. Experts work through it.

## The Confusion Curve

In any non-trivial codebase, your understanding looks like:

```
understanding
     ^
     |              ___---'''
     |          ___-
     |        _-
     |      _-
     |    _-
     |  _-
     |_-
     +----------------------> time
```

The first hours are flat. Then it climbs. The flat part is where most
people give up or thrash.

**Knowing the curve looks like this is half the work.** The other half is
habits to keep moving forward while flat.

## What "Confused But Productive" Looks Like

- You don't fully understand the file, but you've identified its
  imports, types, and public functions.
- You don't know *why* a function works that way, but you know *what*
  it does.
- You're tracing one execution path you can't fully explain, but you
  can describe what comes in and what goes out.
- You have open questions in your scratchpad and you keep asking.

Compare to **stuck-confused**:

- Staring at the same file for an hour.
- Not making notes.
- No specific question you're answering.
- "I just don't get it" as the dominant feeling.

## Strategies to Stay Productive

### 1. Ask one question at a time

Instead of "understand this module," try "find out where the database
connection is opened."

A specific question has a specific answer. "Understand this module"
has no exit criterion.

### 2. Time-box hard reading

Set a timer. 30 minutes of focused reading, then break. Often the
"break" insight is more valuable than the reading.

### 3. Skip down, return up

When a function calls something you don't understand, you have three
moves:

1. **Skip and continue** — assume it does what its name suggests; come back later.
2. **Drop in and read** — full descend, deep tree.
3. **Note and continue** — write a question, keep going.

Beginners always pick (2). Experts pick (1) or (3) most of the time.

### 4. Read at multiple levels

If line-level reading isn't clicking:

- Zoom out to function-level (just signatures).
- Or to file-level (just outline).
- Or to module-level (just imports).

Find the altitude where things *do* make sense. Then descend.

### 5. Run the code

If you've been reading for 30 minutes with no breakthrough, *run* the
code. Set a breakpoint. Watch a variable. The mismatch between what you
expect and what happens often produces the insight.

### 6. Talk to someone — or to your scratchpad

"Rubber duck" debugging works because articulating forces structure.
Open your scratchpad and write:

```
I'm confused because:
- I expected X to happen in foo(), but bar() is called instead.
- I don't see how the data gets from A to B.

What I know:
- foo() takes Input, returns Output.
- bar() is called from somewhere I haven't found.
- The Output of foo() has a field X.

What I'll try next:
- Find callers of bar(); maybe it's wired up at app startup.
```

Writing makes you produce a thought; staring lets you reuse the same
unproductive thought.

## When Confusion Means Something

Sometimes your confusion is correct: the code *is* unclear.

Signs:
- You've spent an hour, taken breaks, asked clarifying questions, and
  still don't get it.
- A maintainer also can't explain it briefly.
- Tests don't cover what you're trying to understand.
- `git blame` shows the author was confused too (commit message: "I
  think this works now").

In these cases, the code itself is the problem. Your contribution might
be:

- A clarifying comment.
- A renaming.
- A test case that pins behavior.
- A larger refactor proposal.

But: see [chestertons-fence.md](chestertons-fence.md) before refactoring.
Sometimes "unclear" is actually "subtle but correct."

## Confusion Hierarchy

When confused, classify *what kind* of confusion:

1. **Language confusion** — I don't understand this syntax / construct.
   → Docs for the language, AI tutor, or a tutorial chapter.

2. **Framework / library confusion** — I don't understand what
   `useEffect`, `await`, `@Decorator` etc. is doing.
   → Framework docs, ideally official.

3. **Codebase convention confusion** — I don't understand why this
   project does X this way.
   → Other examples in the repo, an ADR, or a maintainer.

4. **Domain confusion** — I don't understand what an "FX swap" or
   "invoice line" is.
   → Domain docs, glossary, subject-matter expert.

5. **Genuine bug / unclear code confusion** — the code itself is wrong
   or under-specified.
   → Ask, then maybe fix.

Different confusions need different responses. Identifying the type
helps you escape faster.

## The "Just Use It" Move

For confusing libraries or APIs:

- Don't try to fully understand it.
- Copy a working usage.
- Modify minimally.
- Run; observe; iterate.

You'll absorb the *shape* of correct use without fully understanding
internals. That's often enough for the task at hand.

## Confidence Calibration

Beginners feel either fully confident or fully lost. Experts develop
**calibrated confidence**:

- "I'm 80% sure this function is pure — let me confirm before relying on it."
- "I'm 30% sure I understand this — I'll add a test that pins my
  assumption."
- "I'm 5% sure — let me ask before doing anything irreversible."

Saying "I'm not sure" out loud (in PR descriptions, on Slack) is a
strength signal, not a weakness. Faking confidence and shipping broken
code is the actual weakness.

## When to Step Away

If you've been stuck for ~3 hours with no progress:

- Take a break (real break — coffee, walk, sleep on it).
- The unconscious mind is bizarrely good at this; not metaphor.

If you've been stuck for ~3 *days*:

- Ask someone. Don't keep grinding.
- Ego is a luxury you can't afford this long.

## See Also

- [reading-strategies.md](reading-strategies.md) — when to use which strategy
- [note-taking.md](note-taking.md) — capturing where you got stuck
- [../09-unknown-tech/](../09-unknown-tech/) — confusion specific to new tech
- [../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md) — the meta-confusion
