# 04 — Reproducing Issues

> You cannot reliably fix what you cannot reliably reproduce.

This section is about the discipline of bug repro — turning a vague
report into a deterministic failure you can debug.

## Contents

| File | What you'll learn |
|---|---|
| [minimal-reproduction.md](minimal-reproduction.md) | Building an MRE |
| [environment-parity.md](environment-parity.md) | Matching versions, OS, deps |
| [bisecting.md](bisecting.md) | `git bisect` for regressions |
| [observability.md](observability.md) | Logs, traces, metrics, stack traces |
| [when-you-cant-repro.md](when-you-cant-repro.md) | Strategies for unreproducible bugs |
| [heisenbugs.md](heisenbugs.md) | Race conditions and timing-sensitive bugs |

## The One-Sentence Summary

> **Reduce until it fails every time, in the smallest context, in the
> shortest time.**

## See Also

- [../05-fixing-issues/](../05-fixing-issues/) — once you can repro, you can fix
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md) — deeper debugger usage
