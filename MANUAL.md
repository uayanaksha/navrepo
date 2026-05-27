# MANUAL — One-Page Quick Reference

A compressed cheat-sheet of every section. For depth, follow the links
into the folders.

---

## 1. Orienting in a new repo → [`01-orientation/`](01-orientation/)

- **30-min recon**: README → build → run tests → skim tree → CHANGELOG.
- **Files that talk**: README, CONTRIBUTING (twice), CHANGELOG, CODEOWNERS,
  ARCHITECTURE, ADRs, `.github/`, LICENSE, SECURITY.
- **Read the pulse**: commit frequency, PR cycle time, `git shortlog -sn`.

## 2. Navigating code → [`02-navigation/`](02-navigation/)

- **Tool hierarchy**: LSP → ripgrep → ast-grep → git pickaxe → cross-repo search.
- **Find your three entry points**: process, tests, build.
- **Git as a time machine**: `git log -S 'needle'`, `git log -L :fn:file`,
  `git bisect run`.

## 3. Reading code → [`03-reading-code/`](03-reading-code/)

- **Two passes**: skim for shape, then read for meaning.
- **Types and tests first**, bodies second.
- **Chesterton's fence**: weird code is usually a bugfix you haven't found yet.
- **Tolerance for confusion** is the actual skill.

## 4. Reproducing issues → [`04-reproducing-issues/`](04-reproducing-issues/)

- Build an **MRE**: smallest script that fails every time.
- **Bisect** regressions: `git bisect run ./repro.sh`.
- **Observability first**: logs, traces, stack traces — before code-reading.
- **Heisenbugs** = race / optimizer / UB. Not "fixed by logging."

## 5. Fixing issues → [`05-fixing-issues/`](05-fixing-issues/)

- **Root cause vs symptom**: ask "why" five times.
- **Failing test first** when feasible.
- **Don't bundle**: bugfix PRs do one thing.
- **Backwards compatibility** is part of the fix, not an afterthought.

## 6. Contribution workflow → [`06-contribution/`](06-contribution/)

- Read CONTRIBUTING.md **twice**.
- Search **closed** issues/PRs before filing.
- **Issue first** for anything > a trivial fix.
- **DCO / CLA / license** are real: handle them up front.

## 7. Pull requests → [`07-pull-requests/`](07-pull-requests/)

- **Small** beats clever. 200–400 lines max for fast review.
- **Description** sells the diff: summary, motivation, changes, test plan, risks.
- **Self-review** before requesting review.
- **Draft PRs** are RFCs in disguise.
- Long-running PRs **decay** — rebase weekly, respond promptly.

## 8. Maintainers → [`08-maintainers/`](08-maintainers/)

- Default: **patience, good faith, public**.
- Ping at 2 weeks (gently), close at 6 (gracefully).
- Disagreement: **ask before defending**.
- "No reply for months" usually means "soft no."

## 9. Unknown tech → [`09-unknown-tech/`](09-unknown-tech/)

- Learn **just enough** to ship; trust the rest will fill in.
- **LSP is your tutor.** Hover everything.
- Search the repo for existing examples — patterns teach faster than docs.
- When stuck: build a **toy** version from scratch.

## 10. Features and refactors → [`10-features-refactors/`](10-features-refactors/)

- **Propose first.** One paragraph beats one wasted week.
- **Spikes** are throwaway by definition.
- Refactors should be **invisible** at the API.
- **Deprecation paths**: add → mark → wait → remove.

## 11. Tooling → [`11-tooling/`](11-tooling/)

- LSP, `rg`, `fd`, `fzf`, `bat`, `delta`, `gh`, `jq`, `zoxide`.
- Git config: aliases, `rerere`, `--force-with-lease`.
- **Run CI locally** before pushing.
- AI tools: helpful — but verify everything load-bearing.

## 12. Mindset → [`12-mindset/`](12-mindset/)

- **Read 10x more than you write.**
- No drive-by changes. No bikeshedding.
- Review feedback is about the code, not you.
- **Activity ≠ progress.** Quiet weeks of deep work matter most.

## 13. Hidden knowledge → [`13-hidden-knowledge/`](13-hidden-knowledge/)

- **Governance** docs reveal who decides.
- **Open-core** projects have commercial pressure shaping decisions.
- **License math** matters when copying code.
- **Time zones** explain "silent" maintainers.
- **Security issues** → never public; follow `SECURITY.md`.
- **Conference talks** are undocumented roadmaps.

## 14. Advanced → [`14-advanced/`](14-advanced/)

- **Giving** good code review — the higher-leverage skill.
- Monorepos, distributed systems, legacy code each need their own playbook.
- **Performance investigation**: measure before optimizing.
- **Incident response** discipline transfers between teams.

## 15. Language ecosystems → [`15-language-ecosystems/`](15-language-ecosystems/)

- Per-stack tips: Python, JS/TS, Go, Rust, Java/Kotlin, C/C++.
- Tooling conventions, debugger choice, common pitfalls.

## Appendix → [`appendix/`](appendix/)

- First-day checklist
- Pre-PR checklist
- Command cheatsheet
- PR-discussion phrases
- Red flags (in repos and in your own behavior)
- Glossary
- Further reading

---

### Five rules underneath all of this

1. The repo is a map. Most failure is navigational.
2. Read more than you write.
3. Small focused PRs are kindness.
4. Maintainers are human.
5. Habits compound. Heroics don't.
