# The Right-Repo Problem

You hit a bug while using project X, so you file it against X. But the
bug is often in one of X's *dependencies*, not in X itself. Filing in
the wrong repo wastes everyone's time and delays the fix. Locating the
actual owner of the broken code is a core skill.

## Why the Bug Is Often Elsewhere

Modern software is a deep stack of dependencies. When something breaks,
the symptom surfaces in the app you're running, but the *cause* can live
many layers down:

```
your code
  └─ framework you're using        ← symptom appears here
       └─ a library the framework uses
            └─ a transitive dependency   ← bug actually lives here
                 └─ a native/system library
```

The stack trace shows where it *surfaced*; the bug is wherever the
faulty logic actually is — frequently not the top frame.

## Reading the Stack Trace for Ownership

The trace usually tells you which layer owns the failing code. Look at
the frames and their *file paths / package names*:

```
  File ".../myapp/handlers.py", line 40, in handle      ← your code
  File ".../requests/sessions.py", line 700, in send    ← the `requests` library
  File ".../urllib3/connectionpool.py", line 800        ← urllib3 (a dep of requests)
  File ".../urllib3/util/ssl_.py", line 450             ← bug is most likely HERE
```

The deepest frame *in someone else's package* before the error is your
prime suspect's owner. If the failing line is in `urllib3`, the bug
report belongs to `urllib3`, not your app and not `requests`.

## The Manifest Tells You Who Owns What

To find a dependency's home, read the project's manifest and lockfile:

| Ecosystem | Manifest | Lockfile (exact versions + sources) |
|---|---|---|
| Node | `package.json` | `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock` |
| Python | `pyproject.toml` / `requirements.txt` | `poetry.lock`, `uv.lock` |
| Rust | `Cargo.toml` | `Cargo.lock` |
| Go | `go.mod` | `go.sum` |
| Java | `pom.xml` / `build.gradle` | (resolved tree) |
| Ruby | `Gemfile` | `Gemfile.lock` |

```bash
# Who provides this package, and where does it live?
npm view <pkg> repository.url
pip show <pkg> | grep -i home-page
cargo search <pkg>            # or look it up; Cargo.toml lists the source
go list -m -u all | grep <module>

# Where is a transitive dep coming from? (why is it even here?)
npm why <pkg>          # or: npm ls <pkg>
pnpm why <pkg>
cargo tree -i <pkg>    # inverse tree: who depends on it
go mod why <module>
pipdeptree -r -p <pkg>
```

`cargo tree -i`, `npm why`, and `go mod why` are the key tools: they
answer "where did this dependency come from and who pulls it in," which
tells you the chain of ownership.

## Confirming Before You File

Before reporting upstream, confirm the bug really is there:

1. **Reproduce against the dependency directly**, outside your app, if
   you can. A minimal repro using *just* the suspect library is gold
   (see [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md)).
2. **Check the dependency's version.** Is it old? The bug may already be
   fixed upstream — try the latest version before filing anything.
3. **Search the dependency's tracker.** It may be a known issue with a
   workaround or pending fix (see
   [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)).

## Where to File Once You Know

- **Bug in a dependency:** file in the *dependency's* repo, with a repro
  that uses the dependency directly. Don't file it against the app that
  merely surfaced it.
- **App passes bad input to the dependency:** that's the *app's* bug —
  the dependency is behaving correctly. File against the app.
- **Bug at the integration boundary:** sometimes it's genuinely the
  app's *use* of the dependency. File against the app, referencing the
  dependency behavior.
- **You're not sure which layer:** file against the layer you can
  reproduce at, and *say* you're unsure — "this surfaces in X but may be
  a Y bug; repro attached." Maintainers can redirect.

A security bug in a dependency follows the dependency's *private*
disclosure channel, not a public issue (see
[security-disclosure.md](security-disclosure.md)).

## The Vendor/Patch Reality

When the bug *is* upstream but you need a fix *now* and upstream is slow:

- **Pin to a version without the bug**, if one exists.
- **Apply a local patch** (`patch-package` for npm, `[patch]` in Cargo,
  a Go `replace` directive, a vendored copy) as a stopgap.
- **Still file/PR upstream** — the local patch is temporary; the real
  fix belongs in the owner's repo so everyone benefits and you can drop
  your patch later.

## Anti-Patterns

### Filing the symptom's location, not the cause's

Reporting against the app you were running when the real bug is three
dependencies down. Read the stack trace; find the owning layer first.

### Not checking if it's already fixed upstream

Filing a bug that the dependency fixed two releases ago. Update to the
latest version of the suspect dependency before reporting anything.

### Filing against a transitive dep you can't reproduce in isolation

If you can't reproduce the bug using the dependency directly, you may be
wrong about where it lives. Reproduce at the layer before you file
there.

### Patching locally and never reporting upstream

Your local patch fixes you, but everyone else still hits it — and you
carry the patch forever. File/PR upstream so the fix lands at the
source.

## See Also

- [security-disclosure.md](security-disclosure.md)
- [../04-reproducing-issues/minimal-reproduction.md](../04-reproducing-issues/minimal-reproduction.md)
- [../02-navigation/dependency-traversal.md](../02-navigation/dependency-traversal.md)
- [../06-contribution/search-before-filing.md](../06-contribution/search-before-filing.md)
