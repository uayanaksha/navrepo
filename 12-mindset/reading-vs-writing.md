# Reading vs Writing

You will spend far more of your career reading code than writing it.
Optimize for that ratio. The developer who reads well ships correct
changes; the one who only writes well ships plausible ones.

## The 10x Reading Rule

The common estimate is that we spend roughly **ten times as long
reading code as writing it** — and that's just your own code. On an
unfamiliar codebase, before you write a single line, you're reading to
understand what's there. The ratio skews even further.

The implication is uncomfortable but freeing:

- Time spent getting *faster and better at reading* has more leverage
  than time spent typing faster.
- A change that's quick to write but hard for the next person to read
  is a bad trade — they'll pay the 10x.
- "Clever" code optimizes the 1; "clear" code optimizes the 10.

## Reading Is an Active Skill

Reading code well is not passively letting your eyes pass over it. It's
an active, hypothesis-driven activity:

- You predict what a function does, then check.
- You trace a value from where it's set to where it's used.
- You ask "what happens if this is null / empty / huge?"
- You build a mental model and test it against the next file.

This is a *learnable* skill that most people never deliberately
practice. The techniques are in [../03-reading-code/](../03-reading-code/) —
this page is about valuing the skill enough to invest in it.

## Budget Deliberate Reading Time

Most developers treat reading as something they do *while* trying to
write. Reserve time to *only* read, with no edit pressure:

- Before a feature, read the subsystem you'll touch end-to-end.
- When you join a project, read the core path before changing anything.
- When you admire a project, read its source the way you'd read a book
  by an author you want to learn from.

An hour of pure reading up front routinely saves a day of writing the
wrong thing.

## Writing for the Reader

Since the reader pays the 10x, write for them:

| Optimize for the writer | Optimize for the reader |
|---|---|
| Terse, clever one-liners | Obvious, boring code |
| Implicit magic | Explicit flow |
| Saving keystrokes with abbreviations | Names that say what they mean |
| "I'll remember why" | A comment on the *why* of the non-obvious |
| Big multi-purpose functions | Small single-purpose ones |

The reader you're writing for is often *you*, six months from now, with
no memory of today's context.

## Reading Before Writing on a New Codebase

The instinct on an unfamiliar repo is to start typing. Resist it. The
order that works:

1. **Read** the entry points and the core path (where does a request /
   command / build actually go?).
2. **Read** the tests for the area you'll change — they encode the
   contract.
3. **Read** the git history of the files you'll touch — why are they
   the way they are?
4. *Then* write, matching the patterns you found.

Code that matches the surrounding style is easier to review, more
likely to be correct, and more likely to be merged.

## The Compounding Return

Every codebase you read deeply teaches you patterns you'll recognize
elsewhere. Reading a well-built project is one of the highest-leverage
forms of learning available — better than most tutorials, because it's
real, complete, and battle-tested. See
[../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md).

## Anti-Patterns

### Writing before understanding

Producing a change to code you don't understand is how subtle bugs and
broken assumptions get introduced. If you can't explain the existing
code, you're not ready to change it.

### Optimizing your own typing speed

Faster typing saves seconds. Better reading saves hours. The bottleneck
is almost never how fast you can type.

### Clever code that you'll have to re-read

If *you* have to puzzle out your own code a month later, everyone else
does too, every time. Boring and clear beats clever and dense.

### Skipping the tests when reading

Tests are the fastest route to understanding intended behavior. Reading
the implementation without the tests is reading half the story.

## See Also

- [../03-reading-code/reading-strategies.md](../03-reading-code/reading-strategies.md)
- [../03-reading-code/tests-as-docs.md](../03-reading-code/tests-as-docs.md)
- [../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md)
- [no-drive-bys.md](no-drive-bys.md)
