# Resume Plan — navrepo manual generation

This file lets a fresh session pick up exactly where the previous one
left off. It captures **what was built**, **what's pending**, and the
**style conventions** to keep the manual consistent.

---

## Project context

User asked for a comprehensive field manual on navigating open-source
and enterprise repositories. Initially a single `MANUAL.md`; then asked
to break into a folder structure for depth and breadth.

Root layout:

```
/home/ayanaksha/g/navrepo/
├── README.md                  ← top-level index (DONE)
├── MANUAL.md                  ← one-page TL;DR (DONE)
├── CLAUDE.md                  ← this file
├── 01-orientation/            ← DONE
├── 02-navigation/             ← DONE
├── 03-reading-code/           ← DONE
├── 04-reproducing-issues/     ← DONE
├── 05-fixing-issues/          ← DONE
├── 06-contribution/           ← DONE
├── 07-pull-requests/          ← DONE
├── 08-maintainers/            ← DONE
├── 09-unknown-tech/           ← DONE
├── 10-features-refactors/     ← DONE
├── 11-tooling/                ← PARTIAL (3 of 8 files)
├── 12-mindset/                ← PENDING
├── 13-hidden-knowledge/       ← PENDING
├── 14-advanced/               ← PENDING
├── 15-language-ecosystems/    ← PENDING
└── appendix/                  ← PENDING
```

---

## Resume from here

### Section 11 — Tooling (PARTIAL)

Done:
- `11-tooling/README.md`
- `11-tooling/editor-and-lsp.md`
- `11-tooling/shell-and-cli.md`

Still needed:
- `11-tooling/git-config.md` — aliases, rerere, hooks, useful global config (`format.signOff`, `pull.rebase`, `push.autoSetupRemote`, `diff.algorithm = histogram`, `merge.conflictstyle = zdiff3`)
- `11-tooling/local-ci.md` — pre-commit, `act` for GitHub Actions, mirroring CI locally, language-specific watch tools
- `11-tooling/ai-tools.md` — honest scope (helps with: explaining unfamiliar code, boilerplate, draft tests, exploring search terms; doesn't help with: load-bearing code without verification, multi-file refactors in untyped langs, anything security/financial-sensitive); verify everything; hallucinated APIs are real
- `11-tooling/pr-reviewing-tools.md` — `gh pr checkout`, GitHub "viewed" checkboxes, reviewing in your editor, `git diff main...HEAD --stat` for scope, when to actually run the branch
- `11-tooling/debugging-tools.md` — sanitizers (ASan, TSan, MSan), `strace`/`dtrace`, `lsof`, `perf`, `pprof`, `py-spy`, `rr` (record-and-replay on Linux), Valgrind, language-specific debuggers (`dlv`, `pdb`, `lldb`)

### Section 12 — Mindset (PENDING)

Files to create:
- `12-mindset/README.md`
- `12-mindset/reading-vs-writing.md` — the 10x reading rule; deliberate reading time
- `12-mindset/no-drive-bys.md` — focused changes; capturing "while I'm here" into separate issues
- `12-mindset/bikeshedding.md` — recognize, avoid, don't be the bikeshedder *or* the bikeshed-able
- `12-mindset/receiving-review.md` — review is about code, not you; ack before defend; 1-hour rule before replying when flushed
- `12-mindset/premature-optimization.md` — the real rule: profile first; benchmark numbers in PR or no merge; keep simple version available
- `12-mindset/activity-vs-progress.md` — quiet weeks of deep work; not commenting for visibility
- `12-mindset/be-the-contributor.md` — be the contributor you wish you had
- `12-mindset/imposter-syndrome.md` — calibrated confidence vs faking; "I'm not sure" is a strength signal

### Section 13 — Hidden Knowledge (PENDING)

Files to create:
- `13-hidden-knowledge/README.md`
- `13-hidden-knowledge/governance.md` — BDFL, TSC, working groups, foundations (CNCF/ASF/LF); reading `GOVERNANCE.md`
- `13-hidden-knowledge/maintainer-calculus.md` — maintenance burden, surface area, compatibility, direction, trust
- `13-hidden-knowledge/hidden-roadmaps.md` — roadmap lives in heads, private slacks, conference talks; check recent talks before proposing
- `13-hidden-knowledge/open-core-dynamics.md` — companies behind projects; feature gating; license changes (BSL, SSPL); paying customers outrank you
- `13-hidden-knowledge/license-math.md` — MIT/BSD/Apache permissive; LGPL/MPL weak copyleft; GPL/AGPL strong copyleft; BSL/SSPL source-available; compatibility direction (more restrictive → less restrictive = NO)
- `13-hidden-knowledge/time-zones.md` — calibrating maintainer responsiveness by timezone
- `13-hidden-knowledge/docs-as-contribution.md` — high-leverage, low-stakes; missing setup steps; examples; the fastest trust-building path
- `13-hidden-knowledge/security-disclosure.md` — never public; `SECURITY.md`; coordinated disclosure; CVE process basics
- `13-hidden-knowledge/merge-strategies.md` — squash vs merge vs rebase per project (cross-link 07-pull-requests/commit-history-management.md)
- `13-hidden-knowledge/issue-closure-reasons.md` — fixed, dup, by design, won't fix, stale, no repro
- `13-hidden-knowledge/notable-forks.md` — io.js → Node, Forgejo ← Gitea, OpenTofu ← Terraform, etc.; what they teach
- `13-hidden-knowledge/burnout.md` — recognizing maintainer burnout in projects (cross-link 08-maintainers/burnout-awareness.md but deeper systemic view)
- `13-hidden-knowledge/right-repo-problem.md` — bug may be in dep not in the repo you found it; `package.json`, `go.mod`, `Cargo.toml` tells you the owner
- `13-hidden-knowledge/saying-no.md` — gracefully declining maintainer asks; "happy to close if this doesn't fit" pattern
- `13-hidden-knowledge/conference-talks.md` — undocumented roadmap; watch recent talks before proposing major changes
- `13-hidden-knowledge/compounding-reading.md` — read changelogs, postmortems, one new project a month

### Section 14 — Advanced (PENDING)

Files to create:
- `14-advanced/README.md`
- `14-advanced/giving-code-review.md` — being the reviewer; nits vs blockers; suggesting alternatives; reviewing the description before the diff
- `14-advanced/reviewing-large-prs.md` — checking out locally; reading the test plan first; section-by-section; when to ask for splits
- `14-advanced/working-in-monorepos.md` — code ownership boundaries, build systems (Bazel, Buck, Nx, Turborepo), partial checkouts, scoped CI
- `14-advanced/working-in-distributed-systems.md` — correlation IDs, distributed tracing, eventual consistency, idempotency, retries, exactly-once myth
- `14-advanced/working-with-legacy-code.md` — characterization tests, "strangler fig" pattern, seam-finding, Michael Feathers' framing
- `14-advanced/debugging-toolkit-deep-dive.md` — profilers, flame graphs, eBPF, `rr` time-travel, core dumps, Wireshark
- `14-advanced/performance-investigation.md` — measure first; flame graphs; allocation profiling; hot paths; cache effects; the 5%-95% rule
- `14-advanced/reading-academic-papers.md` — three-pass reading (Keshav); separating contribution from background; implementing toy versions
- `14-advanced/onboarding-others.md` — being a mentor; pairing protocols; writing "first contribution" docs
- `14-advanced/building-reputation.md` — multi-year arc; consistent quality; reciprocity; speaking/writing
- `14-advanced/incident-response.md` — runbooks, war rooms, comms during incident, blameless postmortems, action items

### Section 15 — Language Ecosystems (PENDING)

Files to create:
- `15-language-ecosystems/README.md`
- `15-language-ecosystems/python.md` — uv/poetry/pip; pyenv/uv-managed Pythons; pytest; ruff/black; type checkers (mypy, pyright, basedpyright); virtualenvs; gotchas (mutable defaults, GIL, packaging)
- `15-language-ecosystems/javascript-typescript.md` — npm/pnpm/yarn/bun; tsconfig; eslint/prettier; bundlers (esbuild, vite, webpack); monorepos (pnpm workspaces, Turborepo, Nx); gotchas (this binding, equality, async)
- `15-language-ecosystems/go.md` — modules; gofmt; go test; pprof; staticcheck/golangci-lint; gotchas (nil interface, loop variable, goroutine leaks)
- `15-language-ecosystems/rust.md` — cargo; rustup; clippy; rust-analyzer; cargo-expand; gotchas (lifetimes, ownership, async ecosystem split)
- `15-language-ecosystems/java-kotlin.md` — Maven/Gradle; JetBrains; build scans; profilers; gotchas (classpath, JDK versions, reflection)
- `15-language-ecosystems/cpp.md` — CMake/Bazel; compiler choice; sanitizers; package managers (vcpkg, Conan); gotchas (UB, lifetime, ODR)
- `15-language-ecosystems/multi-language-monorepos.md` — Bazel/Buck for heterogeneous; per-language tooling sandboxing; CODEOWNERS by path; cross-language refactor strategies

### Appendix (PENDING)

Files to create:
- `appendix/README.md`
- `appendix/first-day-checklist.md` — full list from manual section 1
- `appendix/pre-pr-checklist.md` — full list (cross-link 05-fixing-issues/pre-push-checklist.md)
- `appendix/command-cheatsheet.md` — all the `git log -S`, `git log -L`, `git bisect run`, `gh pr ...`, `rg`, etc. in one place
- `appendix/pr-discussion-phrases.md` — pushing back gently, conceding, pausing, uncertain, closing
- `appendix/red-flags.md` — in a repo you're evaluating + in your own behavior
- `appendix/glossary.md` — DCO, CLA, RFC, ADR, MRE, CODEOWNERS, BDFL, TSC, MRE, etc.
- `appendix/further-reading.md` — books, blogs, talks worth knowing about (be specific but light — avoid hallucinated titles)

---

## Style conventions (keep consistent)

### Each topic file follows this shape

1. **Title** (H1).
2. **1–3 sentence framing** at top — what this file is.
3. **Body** in H2/H3 sections.
4. **"See Also" section at the bottom** with relative-path links to
   related files (use `../section-NN/file.md` form).

### Each section's `README.md`

- One-line opening quote-style statement (italicized blockquote).
- A 2-3 sentence intro.
- A `Contents` table with `File | What you'll learn` columns.
- A `The One-Sentence Summary` H2 with one-line takeaway.
- A `See Also` section at bottom.

### Voice and pacing

- Direct, opinionated, second-person ("you").
- Practical over theoretical. Real commands. Real paths.
- Tables for comparisons.
- Code blocks with realistic commands (not `<placeholder>` everywhere).
- "Anti-Patterns" section near the end of most files.
- No fluff. ~150–400 lines per substantial topic file.

### Length targets

- Section `README.md`: 30–50 lines.
- Topic file: 150–400 lines depending on depth.
- Don't pad. Don't repeat across files — link instead.

### Cross-links

Every file should link to ~3–5 related files. Cross-link both
"upstream" (the section that comes before) and "downstream" (where to
go next). Encourage non-linear reading.

### What NOT to do

- Don't add emojis.
- Don't add badges, banners, or hero sections.
- Don't invent specific URLs (they may be wrong / hallucinated). It's
  OK to reference well-known projects by name without URLs.
- Don't reference dates that haven't happened.
- Don't write code comments inside example blocks that explain trivial
  things.

---

## Tool / workflow notes for the resume session

- The original task used `TaskCreate` / `TaskUpdate` with one task per
  section. Tasks #1–#11 are completed; #12 (Section 11) is
  `in_progress`; #13–#17 are `pending`. A new session can recreate
  similar tasks or just track inline.
- Write files in batches of 3–6 per message (parallel `Write` calls)
  to keep momentum.
- Update `MEMORY.md`-style indices only if user asks — that's separate
  from this manual.

---

## Resuming

To resume in a new session, just paste this prompt or paraphrase:

> Continue writing the navrepo manual at `/home/ayanaksha/g/navrepo/`.
> Read `CLAUDE.md` first for the pending file list and style conventions.
> Start with section 11's remaining files (git-config.md, local-ci.md,
> ai-tools.md, pr-reviewing-tools.md, debugging-tools.md), then proceed
> through sections 12–15 and the appendix in order.

That's enough to pick up cleanly.
