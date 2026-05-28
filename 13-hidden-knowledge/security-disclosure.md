# Security Disclosure

If you find a security vulnerability, **do not open a public issue.** A
public report tells attackers about the hole before there's a fix. This
one rule, and the process around it, is knowledge every contributor
needs before they need it.

## The Cardinal Rule

> A security vulnerability is reported **privately**, never in a public
> issue, PR, commit message, or chat.

A public disclosure of an unpatched vulnerability is called a "0-day" —
it hands working attack information to everyone, including bad actors,
while users are still exposed. Even a public PR titled "fix auth bypass"
is a disclosure: anyone watching the repo now knows the bug exists and
roughly where.

When you find something that looks security-relevant, *stop* before
filing it the normal way.

## How to Report Properly

### 1. Find the disclosure channel

Look, in order:

```bash
cat SECURITY.md            # the canonical place; usually has contact + policy
cat .github/SECURITY.md
```

- **`SECURITY.md`** is the standard file. It tells you where and how to
  report — an email, a form, or a platform feature.
- **GitHub "Private vulnerability reporting"** — many repos enable a
  private security advisory channel (under the Security tab). This is
  built for exactly this.
- **A security email** like `security@project.org` if listed.
- **A bug bounty platform** (HackerOne, etc.) for larger projects.

If none exists, email a maintainer **privately** and ask how they'd like
security issues reported. Do not post the details publicly while you
wait.

### 2. Report privately, with detail

A good private report mirrors a good bug report (see
[../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md)),
plus impact:

- **What** the vulnerability is and where.
- **A reproduction** — exact steps / PoC.
- **Impact** — what an attacker could actually do (read data? RCE?
  privilege escalation?).
- **Affected versions**, if you know.
- **A suggested fix**, if you have one.

### 3. Then wait — quietly

The hard part: you've found something exciting and you can't tell
anyone. That's the job. Premature disclosure endangers real users.

## Coordinated Disclosure

The norm is **coordinated disclosure** (sometimes "responsible
disclosure"): the reporter and maintainer agree on a timeline.

```
You report privately
        │
   Maintainer acknowledges & investigates
        │
   Fix developed privately (sometimes in a private fork)
        │
   Fix released  ──►  CVE published  ──►  Public advisory
        │
   Embargo lifts: details become public (often after users can patch)
```

- A common industry default is a **90-day** disclosure window — the
  reporter agrees to hold public details for up to ~90 days to give the
  maintainer time to fix. Adjust to what `SECURITY.md` states.
- **Embargo:** the agreed quiet period. Honor it. Breaking an embargo
  burns trust and endangers users.
- **Credit:** good projects credit reporters in the advisory. That's
  your recognition — earned by doing it right, not by going public early.

## The CVE Process (Basics)

A **CVE** (Common Vulnerabilities and Exposures) is a standardized,
unique ID for a vulnerability (e.g., `CVE-2024-12345`). You generally
don't need to drive this — the maintainer or a CNA (CVE Numbering
Authority) assigns it — but know the shape:

- A CVE ID is requested (via a CNA — many large projects and GitHub are
  CNAs).
- The vulnerability gets a severity score, usually **CVSS** (0–10).
- An advisory is published (e.g., a GitHub Security Advisory, GHSA)
  describing the issue, affected versions, and the fix.
- Downstream tooling (Dependabot, `npm audit`, `pip-audit`,
  `cargo audit`, `osv-scanner`) then alerts everyone depending on the
  vulnerable version.

Your job as finder: report privately, cooperate, let the maintainer run
the CVE machinery.

## Receiving a Report (If You're the Maintainer)

The flip side, briefly:

- **Have a `SECURITY.md`** so people know where to send things.
- **Acknowledge fast**, even if the fix is slow.
- **Fix privately**, then release; coordinate the disclosure timing.
- **Credit the reporter** and publish a clear advisory so users can
  assess and patch.
- **Don't shoot the messenger** — a good-faith reporter is doing you a
  favor.

## When It's Your Dependency

Often the vulnerability isn't in the repo you're working in — it's in a
dependency (connect this to [right-repo-problem.md](right-repo-problem.md)):

- Report it to the *dependency's* security channel, not your project's
  public tracker.
- If your project is *exposed* via the dependency, coordinate: you may
  need to hold your own fix/advisory until the upstream fix ships.
- Use audit tooling (`npm audit`, `pip-audit`, `cargo audit`,
  `osv-scanner`, Dependabot) to know which of your deps have known
  advisories.

## Anti-Patterns

### Filing a public issue for a vulnerability

The disclosure mistake. You've just told every attacker watching the
repo. Use the private channel.

### A public "fix the security bug" PR

The PR, its diff, and its title disclose the vulnerability before users
can patch. Coordinate a private fix and a timed release instead.

### Bragging before the embargo lifts

Tweeting "found a great bug in X" with details, mid-embargo, endangers
users and burns your credit. Wait for the advisory.

### Sitting on a critical bug because there's no SECURITY.md

No channel isn't a reason to go public *or* to stay silent. Email a
maintainer privately and ask how to report.

### Ignoring dependency advisories

A green build with three critical CVEs in your lockfile is not secure.
Run audit tooling and act on it.

## See Also

- [right-repo-problem.md](right-repo-problem.md)
- [license-math.md](license-math.md)
- [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md)
- [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)
- [../08-maintainers/channels.md](../08-maintainers/channels.md)
