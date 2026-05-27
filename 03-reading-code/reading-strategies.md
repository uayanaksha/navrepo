# Reading Strategies

How you approach a file determines whether you understand it in 5 minutes
or never.

## The Two-Pass Rule

**Never try to deeply understand a non-trivial file on the first read.**
Use two distinct passes.

### Pass 1 — Shape

Goal: build a skeleton mental model. ~2 minutes.

Look at:
- File length.
- Top of file: imports, package/module declarations, file-level docstring.
- Type / class / struct definitions.
- Function signatures (names + parameter/return types).
- The overall *layout* — how many functions? What's exported?

Don't read function bodies yet. Just absorb the shape.

### Pass 2 — Meaning

Goal: understand what specific code does. Variable time.

Now follow one execution path. Read function bodies. Step into calls.
But you do this with the *shape* already in your head, so each body is
a known position on a known map.

### Why this works

Reading top-to-bottom forces you to hold uncertainty about the file's
overall structure while also parsing details. Cognitive load explodes.

The two-pass rule separates "structure understanding" from "detail
understanding," which is what good readers do unconsciously.

## Top-Down Reading

Start at the entry point; descend until you hit your question.

**When to use:**
- You have a high-level question ("How does this system handle X?").
- You're new to the codebase and need orientation.
- You know the domain but not the implementation.

**Process:**
1. Find the entry point (see [../02-navigation/entry-points.md](../02-navigation/entry-points.md)).
2. Read the highest-level function.
3. Step into the call that's relevant.
4. Repeat.

## Bottom-Up Reading

Start at a specific function or symbol; walk outward via callers.

**When to use:**
- You have a specific bug or symbol to investigate.
- You're trying to estimate change blast radius.
- You're verifying assumptions ("is this function pure?").

**Process:**
1. Find the symbol.
2. Read it carefully.
3. Find references — who calls it?
4. Read each caller; understand the contract.
5. Continue outward as needed.

## The Zig-Zag (Real-World Reading)

In practice, you do both, often in alternation:

1. Top-down to find the relevant area.
2. Bottom-up to verify a specific function's contract.
3. Top-down again to see how that contract is used elsewhere.
4. Bottom-up to find an unusual caller.

This sounds chaotic; it's actually how skilled readers operate. The
trick is to keep your scratchpad in sync with your current focus.

## "Read the Whole File" — When and When Not

When *not* to read a whole file:
- The file is 2000 lines and you have one question.
- It's a generated file (look for "auto-generated" headers).
- It's a vendored copy of well-documented external code.

When to read a whole file:
- It's small (< 200 lines) and central to your task.
- It's a type/schema definition where each part matters.
- It's a test file you'll be modifying.
- The codebase has a single critical file you'll work in often.

## The 80/20 of File Importance

In a typical 100-file codebase:
- ~5 files have most of the architectural weight.
- ~20 files have the most active logic.
- ~75 files are leaf code, support code, or tests.

Identify the 5 + 20 fast (via `git log --pretty=format: --name-only |
sort | uniq -c | sort -rn | head -30`). Those are your priority reads.

## Reading Tests First

When approaching an unfamiliar module: **read its tests before its
implementation**.

Reasons:
- Tests show inputs/outputs concretely.
- Tests show what the author cared about (the edge cases they covered).
- Tests show *intended use* — the actual API in motion.

See [tests-as-docs.md](tests-as-docs.md) for depth.

## Reading by Diff

When approaching a recent change:

```bash
git show <sha>
gh pr view <num> --diff
```

…and read the *diff*, not the full file. The diff shows you exactly
what's new, and the surrounding context is loaded into your editor for
deeper exploration if needed.

This is especially powerful for understanding *why* a change was made:
the commit message + diff combine to be surprisingly information-dense.

## Reading Generated Code

Generated code (protobuf, ORM models, codegen output) is usually:
- Verbose.
- Boring.
- Better skipped most of the time.

Read it when:
- You're investigating performance (generated code can have surprising patterns).
- You're debugging serialization issues.
- You're customizing the generator itself.

Otherwise: skip and trust.

## Reading Big Files

If you must read a 5000-line file:

1. Open the document outline (LSP "document symbols," Ctrl+Shift+O).
2. Identify the section headers.
3. Read only the section that matters.
4. Note the dependencies into other sections.

Modern editors fold by symbol — collapse everything, expand only what
you need.

## Anti-Patterns

- **Reading top-to-bottom for understanding.** Use two-pass.
- **Treating every file equally.** 80/20 the codebase.
- **Reading without a question.** Aimless reading is exhausting and
  inefficient. Always have something you're trying to learn.
- **Refusing to skip generated code.** It's there for a reason; trust it.
- **Skipping tests.** Often the best entry into a module.

## See Also

- [types-first.md](types-first.md) — what to read in Pass 1
- [tests-as-docs.md](tests-as-docs.md) — reading order for new modules
- [tolerance-for-confusion.md](tolerance-for-confusion.md) — when you're stuck
- [note-taking.md](note-taking.md) — capturing what you learn
