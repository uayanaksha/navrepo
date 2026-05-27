# Build and Run: Step Zero

A repo you can't build is a repo you can't change. This page is about
getting the project running locally — and the discipline of treating that
as the actual first task, not a chore to skip.

## Why This Comes Before Code Reading

When you can build and run the project, you can:

- Verify changes immediately (the dev loop).
- Add print statements and re-run instead of staring at code.
- Use the debugger.
- Run a single test you've just written.
- Reproduce bugs against your local code.

Without a working build, every step takes 10x longer and you compensate
with guesswork.

## The Standard Sequence

1. **Read the README's setup section.** Follow it exactly the first time.
2. **Match versions.** Language version pins exist for reasons.
3. **Install dependencies.** Use the project's chosen manager, not yours.
4. **Build.** Watch for warnings, not just errors.
5. **Run tests.** The full suite if possible, a fast subset if not.
6. **Run the actual application.** End-to-end, not just unit tests.

Don't proceed to step 6 if any earlier step is broken.

## Version Pinning Files

Each ecosystem has its own way of pinning. Respect them.

| Ecosystem | Pin file | Tool |
|---|---|---|
| Node.js | `.nvmrc`, `package.json#engines` | `nvm`, `fnm`, `volta` |
| Python | `.python-version`, `pyproject.toml`, `runtime.txt` | `pyenv`, `uv`, `rye` |
| Ruby | `.ruby-version`, `Gemfile` | `rbenv`, `rvm` |
| Rust | `rust-toolchain.toml` | `rustup` |
| Go | `go.mod` (the `go` directive) | direct or via `gvm` |
| Java | `.sdkmanrc`, `pom.xml#java.version` | `sdkman`, `jenv` |
| Multi-language | `.tool-versions` | `asdf`, `mise` |

If a project pins to Node 18 and you run on 22, expect breakage. Even
when it "seems to work," subtle behavior can differ.

## Common Build Failures

### "Command not found"

A system dependency is missing. Look in:

- README → "Prerequisites" or "Requirements"
- CI workflows (`.github/workflows/*.yml`) — they install everything from scratch
- Dockerfile / devcontainer.json — the canonical environment
- Brewfile, apt-packages.txt, etc.

The CI install script is often the most accurate reference, because it
must work on a fresh machine every run.

### "Module not found" / "Package not found"

Dependencies not installed, or installed at the wrong version.

- Use the project's package manager (`pnpm` vs `npm` vs `yarn` matters).
- Delete `node_modules` / `.venv` / similar and reinstall fresh if confused.
- Check that lockfile matches manifest (`pnpm-lock.yaml` and `package.json`).

### "Permission denied"

- Not running with required permissions (rare; if needed, the README will say).
- Sock/port already in use.
- File permissions wrong after a `chmod` somewhere.

Don't `sudo` to fix this. Find the actual cause.

### "Cannot connect to X"

The project expects a service running locally — Postgres, Redis, Kafka.

- Check `docker-compose.yml` — usually the answer.
- Check the README's "Local development" section.
- `.env.example` often hints at expected services.

### CI Passes But Local Fails

Almost always one of:

1. Different language version.
2. Different OS (case sensitivity on Linux vs Mac; line endings on Windows).
3. CI installs system deps you don't have.
4. Cached build artifacts on your machine confusing things.

Fix: read `.github/workflows/*.yml` carefully and replicate locally. Or
use the project's devcontainer/Docker setup if provided.

## Use the Project's Container If Provided

Many projects ship a `docker-compose.yml` or `.devcontainer/` setup.
**Use it.** Yes, even if you "prefer your own environment."

Reasons:
- It's what CI uses (often).
- It's what other contributors test against.
- It eliminates "works on my machine" debugging.

If the project doesn't have one and you find yourself fighting setup for
hours: that's a valuable contribution to make. A Dockerfile is often more
welcomed than a feature.

## Setup Speed Hacks

### Use `make`-style entry points

If the project has `make setup`, `just bootstrap`, `npm run dev:setup`,
use them. They encode the maintainer's intended workflow.

### Cache aggressively

- For Docker: use `--cache-from`, BuildKit.
- For language tools: keep their global cache between projects (`~/.cache/uv`, etc.).
- For Node: enable corepack, use the package manager the project pinned.

### Skip optional setup first time

If setup has optional steps (e.g., "install GPU libs for ML support") and
your task doesn't need them, skip. Get to a working build fast.

## The "Sanity Run"

Once built, do a quick sanity test:

- Run the unit tests.
- Run *one* integration test if applicable.
- Start the app and hit one endpoint or render one screen.

This 5-minute step proves your build is real, not just compilable.

## When Setup Just Isn't Working

After ~2 hours of fighting setup with no progress:

1. **Read the setup-related issues** in the issue tracker. Sort by recent.
2. **Look for a Discord/Slack channel** for setup help.
3. **Try the Docker / devcontainer path** if not already.
4. **Match an exact OS** the project supports (a Linux VM if you're on
   Mac/Windows and the project is Linux-first).
5. **Ask** in the project's communication channel — but with details:
   exact commands, exact errors, your OS, your versions.

What not to do: invent workarounds that diverge from the project's
intended setup. Those workarounds will leak into your understanding of
the codebase as quirks that aren't really there.

## After a Successful Build

- Add a personal note to your scratchpad: what worked, what didn't.
- If something was missing from the README, propose a docs PR (a good
  first contribution — see [../13-hidden-knowledge/docs-as-contribution.md](../13-hidden-knowledge/docs-as-contribution.md)).
- Commit your editor/local config separately from project code. Don't
  pollute the project's git status.

## See Also

- [first-contact.md](first-contact.md) — where build fits in the recon
- [../11-tooling/local-ci.md](../11-tooling/local-ci.md) — mirroring CI locally
- [../15-language-ecosystems/](../15-language-ecosystems/) — per-stack setup tips
