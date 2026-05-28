# Glossary

The acronyms and jargon used across this manual and the open-source
world, defined plainly. Skim once; return when a term trips you up.

## Contribution & Legal

| Term | Definition |
|---|---|
| **DCO** | Developer Certificate of Origin — a lightweight per-commit `Signed-off-by` attestation that you have the right to contribute the code. See [../06-contribution/legal.md](../06-contribution/legal.md). |
| **CLA** | Contributor License Agreement — a (sometimes broad) legal agreement granting rights to your contribution; the mechanism that can later enable a relicense. |
| **OSI** | Open Source Initiative — stewards the canonical definition of "open source"; approves licenses. |
| **SPDX** | A standard identifier scheme for licenses (e.g. `MIT`, `Apache-2.0`). |
| **Permissive** | License family (MIT/BSD/Apache) allowing almost any use with attribution. |
| **Copyleft** | License family (GPL/LGPL/MPL/AGPL) requiring derived works to stay open. |
| **Source-available** | Public source but commercially restricted (BSL, SSPL) — *not* OSI open source. |

## Governance & Roles

| Term | Definition |
|---|---|
| **BDFL** | Benevolent Dictator For Life — a single person with final say (often the creator). |
| **TSC** | Technical Steering Committee — a formal body governing a project's technical direction. |
| **SIG** | Special Interest Group — a sub-team owning a domain in a large project (e.g. SIG-Network). |
| **CODEOWNERS** | A file mapping paths to owning people/teams who must review changes there. |
| **MAINTAINER / committer** | Someone with merge rights and responsibility for the project. |
| **Foundation** | A neutral nonprofit (LF, CNCF, ASF, etc.) that owns/hosts a project. |
| **Bus factor** | How many people would have to vanish to doom a project (1 = fragile). |

## Proposals & Docs

| Term | Definition |
|---|---|
| **RFC** | Request For Comments — a written proposal for a significant change, discussed before implementation. |
| **ADR** | Architecture Decision Record — a short doc capturing one architectural decision and its rationale. |
| **PEP / KEP** | Python / Kubernetes Enhancement Proposal — those ecosystems' RFC equivalents. |
| **GOVERNANCE.md** | A repo file documenting how the project is run and decisions are made. |
| **SECURITY.md** | A repo file documenting how to privately report vulnerabilities. |

## Issues, PRs & Review

| Term | Definition |
|---|---|
| **PR / MR** | Pull Request (GitHub/others) / Merge Request (GitLab) — proposed changes for review. |
| **MRE / MCVE** | Minimal Reproducible Example — the smallest code that reproduces a bug. See [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md). |
| **LGTM** | "Looks Good To Me" — an approval. |
| **WIP** | Work In Progress — not ready for merge (often a draft PR). |
| **nit** | A trivial, non-blocking review comment (nitpick). |
| **Draft PR** | A PR opened explicitly as not-yet-ready, to get early feedback or run CI. |
| **Squash / rebase / merge commit** | The three ways a PR's commits land on the main branch. See [../13-hidden-knowledge/merge-strategies.md](../13-hidden-knowledge/merge-strategies.md). |
| **Bikeshedding** | Spending disproportionate energy on trivial details. See [../12-mindset/bikeshedding.md](../12-mindset/bikeshedding.md). |

## Tooling & Navigation

| Term | Definition |
|---|---|
| **LSP** | Language Server Protocol — powers go-to-definition, find-references, etc., in your editor. See [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md). |
| **AST** | Abstract Syntax Tree — a structured representation of code; basis of structural search/refactor tools. |
| **Pickaxe** | `git log -S/-G` — searching history for when a string/regex changed. |
| **Bisect** | `git bisect` — binary search through commits to find which one introduced a bug. |
| **Codemod** | An automated, large-scale code transformation (jscodeshift, comby, etc.). |
| **CI / CD** | Continuous Integration / Continuous Delivery (or Deployment) — automated build/test/release pipelines. |
| **Hermetic build** | A build that depends only on declared, pinned inputs — reproducible regardless of the machine. |

## Versioning & Releases

| Term | Definition |
|---|---|
| **SemVer** | Semantic Versioning — `MAJOR.MINOR.PATCH`; major = breaking, minor = features, patch = fixes. |
| **Conventional Commits** | A commit-message convention (`feat:`, `fix:`, …) that can drive changelogs/versioning. See [../06-contribution/commit-conventions.md](../06-contribution/commit-conventions.md). |
| **Changelog** | A human-readable record of what changed between releases. |
| **Expand-contract** | A migration pattern: add the new alongside the old, migrate, then remove the old. See [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md). |
| **Deprecation** | Marking something as discouraged/scheduled-for-removal while it still works. |

## Security

| Term | Definition |
|---|---|
| **CVE** | Common Vulnerabilities and Exposures — a unique ID for a known vulnerability (e.g. `CVE-2024-12345`). |
| **CVSS** | Common Vulnerability Scoring System — a 0–10 severity score. |
| **CNA** | CVE Numbering Authority — an org authorized to assign CVE IDs. |
| **GHSA** | GitHub Security Advisory — GitHub's vulnerability advisory record. |
| **Coordinated disclosure** | Reporting privately and agreeing on a timeline before going public. See [../13-hidden-knowledge/security-disclosure.md](../13-hidden-knowledge/security-disclosure.md). |
| **0-day** | A vulnerability disclosed/exploited before a fix is available. |
| **Embargo** | The agreed quiet period before a vulnerability's details go public. |

## Distributed Systems & Ops

| Term | Definition |
|---|---|
| **Idempotency** | An operation that has the same effect whether done once or many times. See [../14-advanced/working-in-distributed-systems.md](../14-advanced/working-in-distributed-systems.md). |
| **Correlation ID** | A unique ID propagated through a request's path across services for tracing. |
| **Eventual consistency** | Replicas converge over time; reads may be briefly stale. |
| **Circuit breaker** | A pattern that stops calling a failing service to let it recover. |
| **Backpressure** | Deliberately shedding/queuing load when overwhelmed. |
| **SLO / SLA / SLI** | Service Level Objective / Agreement / Indicator — reliability targets and their measurements. |
| **Runbook** | A pre-written guide for handling a known operational scenario. |
| **Postmortem** | A blameless write-up after an incident: timeline, cause, action items. See [../14-advanced/incident-response.md](../14-advanced/incident-response.md). |

## Language-Specific Hazards

| Term | Definition |
|---|---|
| **GIL** | Global Interpreter Lock (CPython) — prevents true parallel execution of Python bytecode across threads. |
| **UB** | Undefined Behavior (C/C++) — invalid code the compiler may treat arbitrarily. See [../15-language-ecosystems/cpp.md](../15-language-ecosystems/cpp.md). |
| **RAII** | Resource Acquisition Is Initialization — C++ idiom tying resource lifetime to object scope. |
| **ODR** | One Definition Rule — a C++ symbol may be defined only once program-wide. |
| **Borrow checker** | Rust's compile-time enforcer of ownership/borrowing rules. See [../15-language-ecosystems/rust.md](../15-language-ecosystems/rust.md). |
| **Heisenbug** | A bug that changes or vanishes when you try to observe it. See [../04-reproducing-issues/heisenbugs.md](../04-reproducing-issues/heisenbugs.md). |

## Concepts From This Manual

| Term | Definition |
|---|---|
| **Chesterton's Fence** | Don't remove something until you understand why it's there. See [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md). |
| **Strangler fig** | Incrementally replacing a legacy system by routing around it piece by piece. See [../14-advanced/working-with-legacy-code.md](../14-advanced/working-with-legacy-code.md). |
| **Characterization test** | A test that pins down what legacy code *currently* does, as a safety net. |
| **Drive-by** | An unrelated change slipped into a focused PR. See [../12-mindset/no-drive-bys.md](../12-mindset/no-drive-bys.md). |
| **Maintainer calculus** | The cost/benefit a maintainer weighs on every contribution. See [../13-hidden-knowledge/maintainer-calculus.md](../13-hidden-knowledge/maintainer-calculus.md). |

## See Also

- [further-reading.md](further-reading.md)
- [../README.md](../README.md)
- [command-cheatsheet.md](command-cheatsheet.md)
