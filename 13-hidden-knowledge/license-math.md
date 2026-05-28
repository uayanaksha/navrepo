# License Math

A software license decides what you may legally do with code — combine
it, ship it, sell it, keep your changes private. Get this wrong and you
create real legal exposure for yourself or your employer. You don't need
to be a lawyer, but you need the mental model.

> Not legal advice. For anything consequential, ask an actual lawyer.
> This is the engineer's working model.

## The Three Big Families

| Family | Core obligation | Examples |
|---|---|---|
| **Permissive** | Keep the copyright notice; otherwise do almost anything | MIT, BSD, Apache-2.0, ISC |
| **Copyleft** | Share your changes under the same license | GPL, AGPL (strong); LGPL, MPL (weak) |
| **Source-available** | Read/self-host, but commercial use restricted (NOT open source) | BSL, SSPL, "fair source" |

The family tells you 90% of what you need. The specific license fills in
the rest.

## Permissive (MIT / BSD / Apache)

The "do almost anything" licenses. You can use, modify, embed in
proprietary software, and sell — as long as you preserve the copyright
notice and license text.

- **MIT / ISC / BSD:** minimal. Keep the notice; you're done.
- **Apache-2.0:** permissive *plus* an explicit patent grant
  (contributors grant you patent rights) and a `NOTICE` file
  requirement. Preferred for anything where patents might be a concern.

For most contributors and most companies, permissive code is the
no-friction choice. You can build closed-source products on it freely.

## Weak Copyleft (LGPL / MPL)

The "share changes to *this* code, but not your whole app" middle
ground.

- **LGPL:** you can link to an LGPL library from proprietary code, but
  modifications *to the library itself* must be shared. Designed so
  libraries can be used without infecting your application.
- **MPL-2.0:** copyleft at the *file* level. Changes to MPL files stay
  MPL; your other files are unaffected. File-level granularity makes it
  easy to mix.

These let you keep your application proprietary while keeping
improvements to the open component open.

## Strong Copyleft (GPL / AGPL)

The "if you distribute, the whole combined work must be open under the
same terms" licenses.

- **GPL:** if you distribute software that incorporates GPL code, the
  *entire* work must be GPL (source available to recipients). This is
  the "viral" property — deliberate, not accidental.
- **AGPL:** GPL *plus* the network clause. Even offering the software as
  a *service* (no distribution of binaries) triggers the obligation to
  provide source to users. Closes the "SaaS loophole."

**This is where companies get nervous.** Many organizations forbid GPL
(and especially AGPL) in their products because of the obligation to
open-source the combined work. If your employer has a policy, it almost
certainly covers this. Check before pulling GPL/AGPL code into a
proprietary product.

## Source-Available (BSL / SSPL)

**Not open source** (not OSI-approved), despite the public repository.
You can usually read and self-host, but commercial use — especially
offering the software *as a service* — is restricted.

- **BSL** (Business Source License): restricts competing commercial use;
  typically converts to a true open license after a few years.
- **SSPL**: if you offer the software as a service, you must open-source
  your *entire service stack* — designed to block cloud providers from
  reselling.

These exist because of the open-core economics in
[open-core-dynamics.md](open-core-dynamics.md). Treat them as
"commercial license with source visible," not as open source.

## The Compatibility Direction Rule

The single most important practical rule when *combining* code:

> You can move code toward **more** restrictive, never toward **less**.

```
permissive  →  weak copyleft  →  strong copyleft   ✅ (allowed direction)
(MIT)          (MPL/LGPL)        (GPL/AGPL)

strong copyleft  →  permissive                      ❌ (NOT allowed)
(GPL)               (MIT)
```

Concretely:

- You **can** include MIT code in a GPL project (MIT → GPL: fine).
- You **cannot** take GPL code and ship it under MIT (GPL → MIT: no).
- You **cannot** put GPL code into a proprietary product you distribute
  closed-source.
- Apache-2.0 → GPLv2 is famously **incompatible** (patent-clause
  conflict); Apache-2.0 → GPLv3 is fine. The details bite.

When in doubt: combining licenses is exactly where you ask a lawyer.
Mixing copyleft and proprietary is the high-risk zone.

## What to Actually Check

Practical, every-project habits:

```bash
# Find the license
ls LICENSE* COPYING*
cat LICENSE | head -5          # which family is this?

# Check a dependency's license before adding it
npm view <pkg> license
pip show <pkg> | grep License
cargo about / cargo-deny       # Rust: audit dependency licenses
go-licenses                    # Go
```

- **Before adding a dependency:** is its license compatible with your
  project's? `cargo-deny`, `go-licenses`, `license-checker` (npm), and
  similar tools automate this in CI.
- **Before contributing:** the project's license is what your
  contribution falls under. The CLA/DCO governs the rights you grant
  (see [../06-contribution/legal.md](../06-contribution/legal.md)).
- **For business-critical deps:** re-check periodically — licenses
  *change* (see [open-core-dynamics.md](open-core-dynamics.md)).

## Other Things That Aren't Licenses (But Act Like Them)

- **No license at all** = all rights reserved. Code on GitHub *without*
  a license is **not** free to use, despite being public. You have no
  rights to it beyond viewing. Don't copy it.
- **Trademark** is separate from copyright/license. A permissive license
  on the code doesn't give you rights to the project's *name* or logo
  (e.g., you can fork the code but may not be able to call your fork by
  the original name).
- **Patents** ride alongside. Apache-2.0 grants patent rights
  explicitly; MIT is silent on them.

## Anti-Patterns

### Copying unlicensed code

"It's public on GitHub" is not a license. No license = all rights
reserved = you can't use it. Find a licensed alternative.

### Pulling GPL into a proprietary product

The classic compliance violation. If you distribute the product, the
GPL obligation attaches to the whole work. Know your employer's policy.

### Assuming source-available == open source

BSL and SSPL repos are public but commercially restricted. Read the
actual terms before building a business on them.

### Never re-checking dependency licenses

A dependency that was MIT when you added it can relicense. Audit
business-critical licenses periodically, ideally in CI.

### Treating "no license" as permission

The most common mistake. Absence of a license means *no* rights
granted, not *all* rights granted.

## See Also

- [open-core-dynamics.md](open-core-dynamics.md)
- [notable-forks.md](notable-forks.md)
- [../06-contribution/legal.md](../06-contribution/legal.md)
- [security-disclosure.md](security-disclosure.md)
