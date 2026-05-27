# Legal: DCO, CLA, License

The boring stuff that can void your contribution if you skip it.

## DCO — Developer Certificate of Origin

A **lightweight legal acknowledgment** that you have the right to
contribute the code you're submitting. Originated in the Linux kernel.

### What it looks like

```
Signed-off-by: Your Name <your.email@example.com>
```

…appended to your commit message.

### How to sign

```bash
git commit -s -m "your message"
# -s adds Signed-off-by based on git's user.name and user.email
```

Or in IDE: most have an "add sign-off" toggle.

You can also configure globally:

```bash
git config --global format.signOff true
```

…and every commit gets signed.

### What it asserts

The full text is short — read it once at [developercertificate.org](https://developercertificate.org/).

Effectively: "I have the right to contribute this code under the
project's license."

If you're contributing your own original work: easy.

If you're contributing work owned by your employer: you need your
employer's permission (often implicit in employment contracts, but
check).

### What DCO doesn't do

- Doesn't transfer copyright.
- Doesn't grant additional licenses.
- Doesn't waive any rights.

It's an assertion, not a grant.

## CLA — Contributor License Agreement

A **stronger legal document**. More common in corporate-stewarded
projects.

### Common forms

- **Individual CLA**: you grant the project rights to use your
  contributions.
- **Corporate CLA**: your employer signs covering all employee
  contributions.

The grant typically includes:
- License to use, modify, distribute your contribution.
- Patent grant.
- Affirmation that you have the right to contribute.

### How CLA shows up

- A bot comments on your first PR asking you to sign.
- Click through; sign electronically.
- One-time; covers future PRs to that project (or org).

### Read what you sign

CLAs vary. Some are minor; some are sweeping. Examples:

- **Apache CLA**: grants license, patent grant, assertion of right.
  Doesn't transfer copyright. Widely accepted.
- **Google CLA**: similar to Apache's.
- **Asymmetric CLA**: you grant the project relicensing rights.
  Controversial — the project can sell your work under a different
  license. Some contributors decline.

Don't sign without reading at least once. Once signed, it covers all
future contributions.

### When CLA matters for your employer

If you're contributing at work:

- Check your employer's OSS contribution policy.
- Some employers have a process for OSS contributions.
- A CLA might require corporate review.

Better to clear this up before your first PR than after.

## License Compatibility

When **copying or adapting code from elsewhere** into the project, the
licenses must be compatible.

### Common combinations

| Source | Destination | Compatible? |
|---|---|---|
| MIT | MIT | Yes |
| MIT | Apache 2.0 | Yes (Apache is permissive enough) |
| MIT | GPL | Yes (downstream becomes GPL) |
| Apache 2.0 | MIT | **No** (Apache requires NOTICE preservation) |
| Apache 2.0 | GPLv2 | Disputed; GPLv2-only is incompatible |
| Apache 2.0 | GPLv3 | Yes (GPLv3 was made compatible) |
| GPL | MIT | **No** (GPL prevents re-licensing) |
| GPL | Apache 2.0 | **No** |
| AGPL | Anything non-AGPL | Generally **no** for distribution |
| BSL / SSPL | Most OSS | **No** (these are non-OSS by OSI) |

Rule of thumb: **don't copy from a more restrictive license into a less
restrictive one**.

### What "copying" means

- Pasting code: definitely.
- Adapting code with same structure: usually.
- Using as inspiration: typically not (but disclose if unsure).

When in doubt, link to the source in a comment and ask the maintainer.

### Code attribution

When you legitimately copy/adapt:

- Preserve copyright notices.
- Preserve license notices.
- Sometimes add an attribution in `NOTICE` file or similar.

Some licenses (Apache 2.0, BSD with advertising clause) have specific
attribution requirements. Read them.

## Copying Your Own Past Code

You wrote code at $previous_employer; can you contribute it to an OSS
project?

Usually no. Your employer owns work-for-hire code, even after you leave.
Exceptions:

- Your employer published the original code under an OSS license.
- You have explicit written permission.
- The code was clearly outside scope of employment.

When in doubt, write fresh.

## Patent Implications

Some licenses (Apache 2.0, GPLv3) include explicit patent grants. Some
(MIT, BSD) don't.

If you hold patents that read on your contribution, you may want to use
or seek a license that includes a patent grant — protects downstream
users.

For most contributors this doesn't come up. For employees of large
companies, it can.

## AI-Generated Code

A new legal area, evolving fast as of 2026:

- Some projects disallow AI-generated code (training-set ambiguity).
- Some require disclosure.
- Some treat it as your work, subject to DCO.

Check the project's policy. If unstated, ask. Many maintainers prefer
that you disclose if a significant chunk was AI-assisted.

## Crypto Signing

Some projects require commit signing (GPG / SSH):

```bash
git commit -S -m "message"  # GPG-signed
```

Setup:

```bash
gpg --gen-key
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true
```

Or SSH-based:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

GitHub shows "Verified" badges for signed commits. Some projects
require it on `main`.

## Cleaning Up Mistakes

If you forgot DCO sign-off:

```bash
git commit --amend -s --no-edit  # sign-off the last commit
git rebase --signoff main         # sign all commits since main
```

Then force-push (with `--force-with-lease`, not `--force`):

```bash
git push --force-with-lease
```

Force pushes to your own branch on a fork are fine. Don't force push to
shared branches.

## What "OSI-Approved" Means

The Open Source Initiative maintains a list of approved licenses. A
license being OSI-approved is what most people mean by "open source."

Non-OSI licenses (BSL, SSPL, Elastic License) are "source-available"
but not open source by the strict definition. They impose use
restrictions OSI doesn't accept.

Knowing this helps you read claims like "we're open source" with
calibration.

## See Also

- [contributing-md.md](contributing-md.md) — project-specific legal expectations
- [../13-hidden-knowledge/license-math.md](../13-hidden-knowledge/license-math.md) — deeper license discussion
- [commit-conventions.md](commit-conventions.md) — sign-off in commit messages
