# 05 — Fixing Issues

> A fix should make a problem unrepresentable, not invisible.

This section is about the craft of bug fixing: finding root causes,
sizing changes, and not introducing new bugs while fixing old ones.

## Contents

| File | What you'll learn |
|---|---|
| [root-cause-vs-symptom.md](root-cause-vs-symptom.md) | Distinguishing the two; choosing where to fix |
| [five-whys.md](five-whys.md) | Drilling past the obvious answer |
| [test-first-fixing.md](test-first-fixing.md) | Bug fixes start with a failing test |
| [fix-surface-area.md](fix-surface-area.md) | Sizing fixes appropriately |
| [pre-push-checklist.md](pre-push-checklist.md) | Verifying before sharing |
| [backwards-compatibility.md](backwards-compatibility.md) | Fixes that don't break callers |

## The One-Sentence Summary

> **Find the root, fix it once, prove it with a test, ship it small.**

## See Also

- [../04-reproducing-issues/](../04-reproducing-issues/) — the prerequisite
- [../07-pull-requests/](../07-pull-requests/) — shipping the fix
