# navrepo — The Repository Navigator's Manual

A practical, opinionated field guide for working effectively in open-source
and enterprise codebases. From your first clone to a merged PR to a
long-term contributor track.

This is a **field manual**, not a textbook. Every page is meant to be
useful at the moment you'd reach for it. Read linearly if you're new;
skim and jump if you're not.

---

## How to Use This

| You are... | Start here |
|---|---|
| New to the project | [01-orientation](01-orientation/) → [02-navigation](02-navigation/) |
| Trying to read unfamiliar code | [03-reading-code](03-reading-code/) |
| Hunting a bug | [04-reproducing-issues](04-reproducing-issues/) → [05-fixing-issues](05-fixing-issues/) |
| About to open a PR | [06-contribution](06-contribution/) → [07-pull-requests](07-pull-requests/) |
| Stuck dealing with a maintainer | [08-maintainers](08-maintainers/) |
| In a language/framework you don't know | [09-unknown-tech](09-unknown-tech/) → [15-language-ecosystems](15-language-ecosystems/) |
| Planning a big change | [10-features-refactors](10-features-refactors/) |
| Setting up your environment | [11-tooling](11-tooling/) |
| Feeling stuck or burned out | [12-mindset](12-mindset/) |
| Wondering "why isn't this written down?" | [13-hidden-knowledge](13-hidden-knowledge/) |
| Ready to level up beyond contributing | [14-advanced](14-advanced/) |
| Need a checklist *right now* | [appendix](appendix/) |

For a single-page distillation, see [MANUAL.md](MANUAL.md).

---

## Table of Contents

### Part I — Getting Oriented
- [**01-orientation**](01-orientation/) — First contact with a new repo
- [**02-navigation**](02-navigation/) — Moving through code at speed
- [**03-reading-code**](03-reading-code/) — Reading is a separate skill from writing

### Part II — Doing the Work
- [**04-reproducing-issues**](04-reproducing-issues/) — Bug repro methodology
- [**05-fixing-issues**](05-fixing-issues/) — Root causes vs symptoms

### Part III — Shipping Your Work
- [**06-contribution**](06-contribution/) — The workflow before code
- [**07-pull-requests**](07-pull-requests/) — Crafting PRs that merge
- [**08-maintainers**](08-maintainers/) — The human side of OSS

### Part IV — Going Beyond
- [**09-unknown-tech**](09-unknown-tech/) — Learning your way into a new stack
- [**10-features-refactors**](10-features-refactors/) — Larger changes done safely
- [**11-tooling**](11-tooling/) — Tools that compound over a career
- [**12-mindset**](12-mindset/) — Habits and anti-patterns

### Part V — What Nobody Tells You
- [**13-hidden-knowledge**](13-hidden-knowledge/) — The stuff you only learn the hard way
- [**14-advanced**](14-advanced/) — Reviewing, monorepos, distributed systems, legacy
- [**15-language-ecosystems**](15-language-ecosystems/) — Per-stack idioms and traps

### Reference
- [**appendix**](appendix/) — Checklists, cheatsheets, glossary

---

## Conventions Used in This Manual

- **Bold** = the rule. Read this if nothing else.
- `inline code` = a literal command, file, or option you can type.
- ```` ``` ```` fenced blocks = copy-pasteable.
- "The project's CONTRIBUTING.md" = always check yours, this is general advice.

Every section has a `README.md` that summarizes its contents and tells you
which file to open for what.

---

## Philosophy

Five ideas underlie everything in this manual:

1. **The repo is a map.** Most failure is navigational, not technical.
2. **Read more than you write.** 10x is a floor, not a ceiling.
3. **Small, focused PRs are kindness.** To reviewers, to your future self.
4. **Maintainers are human.** Treat OSS as a relationship, not a transaction.
5. **Compounding habits beat heroic effort.** A career is hundreds of small
   reps done well.

If you disagree with one of these, read the section that defends it. If
you still disagree, you've found your edge — work from there.

---

## Contributing to This Manual

This is a living document. If something here is wrong, vague, or missing —
fix it. Apply the same rules the manual describes. The metaphor is
intentional.

---

## License

Treat the contents of this manual as advice, not law. Adapt to your
context. The worst possible reading of this document is to follow it
literally where it doesn't apply.
