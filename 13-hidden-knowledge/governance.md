# Governance

Every project has a power structure that decides what gets merged, what
direction it takes, and who has the final word. Knowing the structure
tells you who to convince and how decisions actually get made.

## Why Governance Matters to You

Before you invest in a big proposal, you need to know:

- **Who decides?** One person? A committee? A vote?
- **How are disputes resolved?** Benevolent dictator? Consensus?
  Formal process?
- **How do you become trusted?** What's the path from contributor to
  committer to maintainer?

Pitching a major change to the wrong person, or expecting a vote where
one person decides (or vice versa), wastes everyone's time.

## Common Governance Models

### BDFL (Benevolent Dictator For Life)

One person has final say. Historically Python (Guido van Rossum, until
he stepped back), Linux (Linus Torvalds for the kernel), many smaller
projects led by their creator.

- **Decisions:** fast, coherent, opinionated.
- **Risk:** bus factor of one; burnout; succession crises.
- **For you:** find out who the BDFL is and what they care about. Their
  taste *is* the project's direction.

### Core team / maintainer group

A small group shares authority. Most mature mid-size projects.

- **Decisions:** by rough consensus among maintainers, sometimes with
  area owners.
- **For you:** identify the owner of the *area* you're touching. A
  network-layer change is decided by whoever owns the network layer.

### Technical Steering Committee (TSC)

A formal body, often elected, with documented authority. Common in
large/foundation-hosted projects (Node.js, Kubernetes, many CNCF
projects).

- **Decisions:** by vote or documented consensus, with meeting minutes.
- **For you:** big changes go through a process (often an RFC or
  enhancement proposal). Read it before proposing.

### Working groups / SIGs

Large projects split into Special Interest Groups or working groups,
each owning a domain (SIG-Network, SIG-Storage in Kubernetes).

- **For you:** find the right SIG for your area; that's where the
  decision and the reviewers live.

### Foundation-hosted

The project is owned by a neutral nonprofit, not a company. Common
foundations:

| Foundation | Examples of what it hosts |
|---|---|
| **Linux Foundation (LF)** | Linux, many infra projects, sub-foundations |
| **CNCF** (under LF) | Kubernetes, Prometheus, Envoy, etcd |
| **Apache (ASF)** | Kafka, Cassandra, Spark, httpd; strong process culture |
| **Eclipse** | Jakarta EE, many Java projects |
| **OpenJS** | Node.js, jQuery, Electron, webpack |
| **Rust Foundation** | stewards Rust's trademarks/infra (not the language design) |

Foundation hosting usually means: a real CLA/DCO, trademark protection,
neutral ownership (no single company can unilaterally relicense — a
protection against the flips in
[open-core-dynamics.md](open-core-dynamics.md)), and a documented
decision process.

## Reading GOVERNANCE.md

Many projects document their structure. Look for:

- `GOVERNANCE.md` — the structure, roles, decision process.
- `MAINTAINERS` / `OWNERS` / `CODEOWNERS` — who owns what.
- `STEERING.md`, `CHARTER.md` — for formal bodies.
- The `CONTRIBUTING.md` — often links to all of the above.

If `GOVERNANCE.md` exists, read it before any non-trivial proposal. It
literally tells you how to get your change accepted.

```bash
# quick scan for governance docs
ls GOVERNANCE.md MAINTAINERS* OWNERS CODEOWNERS CHARTER.md 2>/dev/null
fd -i 'governance|maintainers|owners|charter|steering'
```

## The Proposal Process

Bigger projects formalize how major changes are proposed. Names vary:

| Project | Proposal mechanism |
|---|---|
| Python | PEP (Python Enhancement Proposal) |
| Rust | RFC (in the `rfcs` repo) |
| Kubernetes | KEP (Kubernetes Enhancement Proposal) |
| Go | Proposal process (the `proposal` repo) |
| Ember, React, many others | RFC repo |
| TC39 (JS) | Staged proposal process (Stage 0–4) |

If your change is large, *find the process first*. Submitting a huge PR
to a project that requires an accepted proposal first gets it closed
with "please file an RFC." See
[../10-features-refactors/proposals-and-rfcs.md](../10-features-refactors/proposals-and-rfcs.md).

## Roles and the Trust Ladder

Most projects have an implicit or explicit ladder:

```
user → contributor → regular contributor → committer/maintainer → core/TSC
```

Each rung is earned with consistent, quality work over time. You don't
ask for commit access; you demonstrate the judgment that makes someone
offer it. See [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md).

## Anti-Patterns

### Pitching the wrong person

Lobbying a contributor who can't approve your change, or going over an
area owner's head to the BDFL. Find who actually decides this kind of
change.

### Ignoring the proposal process

Dropping a 2,000-line feature PR on a project that requires an accepted
enhancement proposal. It will be closed unread. Process first.

### Assuming a company == the project

In foundation-hosted projects, no single company controls direction
(by design). Treating it like a vendor's product misreads the power
structure.

### Expecting consensus where there's a dictator (or vice versa)

In a BDFL project, a "vote" in the comments means nothing. In a
consensus project, expecting one person to just decide stalls you.
Match your approach to the model.

## See Also

- [maintainer-calculus.md](maintainer-calculus.md)
- [open-core-dynamics.md](open-core-dynamics.md)
- [hidden-roadmaps.md](hidden-roadmaps.md)
- [../10-features-refactors/proposals-and-rfcs.md](../10-features-refactors/proposals-and-rfcs.md)
- [../14-advanced/building-reputation.md](../14-advanced/building-reputation.md)
