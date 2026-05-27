# LSP Navigation

The Language Server Protocol (LSP) understands code *as code*, not as
text. When you ask "where is `foo` defined?" — LSP answers correctly even
when there are 100 things named `foo`. Text search can't.

If you take one thing from this manual: **get your LSP working in every
language you touch**. It's the single highest-leverage tool.

## What LSP Provides

| Operation | Editor command (common) | What it does |
|---|---|---|
| Go to definition | F12, gd | Jump to the symbol's definition |
| Find references | Shift+F12, gr | List everywhere it's used |
| Go to type definition | — | Jump to the type (not the value) |
| Go to implementation | — | For interfaces: jump to implementors |
| Workspace symbols | Ctrl/Cmd+T | Fuzzy search all symbols in project |
| Document symbols | Ctrl/Cmd+Shift+O | Outline of current file |
| Rename symbol | F2 | Rename everywhere, semantically |
| Hover | mouse hover, K | Show type info and docs |
| Code actions | Ctrl/Cmd+. | Quick fixes, refactors |
| Signature help | Ctrl+Shift+Space | Show function signature while calling |

Bindings vary by editor; learn yours.

## The Three Most Important

Master these three first:

### 1. Go to definition

When you see `widget.process()`, jump to `process`. Then to its types.
Then to *their* definitions. This is the basic navigation primitive.

### 2. Find references

The inverse: from a function, find every caller. Useful for:
- Estimating blast radius before changing a signature.
- Understanding how a utility is used.
- Finding examples of correct usage.

### 3. Workspace symbols

"I know there's a function with `Throttle` in the name somewhere…"
→ Ctrl+T, type `throttle`. Done.

This replaces `grep`-for-function-name in most cases.

## Setting Up LSP

The server is per-language; the client is your editor's plugin.

| Language | Server | Notes |
|---|---|---|
| Go | `gopls` | Comes with Go toolchain since 1.16 |
| Rust | `rust-analyzer` | Install via `rustup component add` |
| Python | `pyright`, `pylsp`, `basedpyright` | Choose one |
| TypeScript/JS | `typescript-language-server`, `vtsls` | Built-in to VS Code |
| Java | `jdtls` | Setup is heavy; use VS Code Java pack |
| Kotlin | `kotlin-language-server` | Limited compared to JetBrains' own |
| C/C++ | `clangd` | Better than older alternatives |
| C# | `omnisharp`, Roslyn LSP | VS Code C# Dev Kit |
| Ruby | `ruby-lsp` | Newer; `solargraph` older |
| Scala | `metals` | High-quality, well-maintained |
| Swift | `sourcekit-lsp` | First-party from Apple |
| Lua | `lua-language-server` | Excellent for Neovim configs |
| Bash | `bash-language-server` | Surprisingly useful |
| YAML | `yaml-language-server` | Schema-aware for CI files, k8s, etc. |
| Terraform | `terraform-ls` | First-party from HashiCorp |
| Markdown | `marksman` | Cross-file link navigation |

Editors vary in how much setup you do manually:
- **VS Code**: usually one extension per language, auto-configured.
- **Neovim**: `nvim-lspconfig` + your servers, more manual.
- **Helix**: server lookup is configured but you install servers.
- **JetBrains IDEs**: their own language tooling, not LSP (usually superior in scope).

## Common LSP Pitfalls

### "Go to definition doesn't work."

Causes, in order of likelihood:

1. **The language server isn't running.** Check editor status.
2. **The project isn't building.** LSP needs to compile to understand.
3. **You're at a path the server doesn't know about.** Open the project
   root, not a subdirectory.
4. **The file is excluded.** Check editor settings.
5. **Generated code isn't generated yet.** Run codegen before opening.

### "Find references misses some."

- **Dynamic dispatch / interface implementations** — LSP often shows
  direct callers, not virtual dispatch sites. Use "Find implementations"
  separately.
- **Reflection / runtime resolution** — LSP can't see these.
- **Cross-language calls** (e.g., FFI, RPC) — outside the server's view.

When LSP misses, fall back to `rg`.

### "It's slow."

- Index may be rebuilding. Wait.
- Project too large? Some servers struggle with monorepos; check their docs.
- Background tasks (formatting, linting) compete for CPU.
- For Rust: `cargo check` running can block `rust-analyzer`.

## Beyond the Basics

### Code actions

LSP code actions include:
- Auto-import.
- Extract function/variable.
- Fix-all on save.
- Generate boilerplate (interface methods, missing imports).

In any new language, spend 15 minutes seeing what code actions are
available. They often automate what you'd otherwise type by hand.

### Refactor: rename symbol

LSP's rename is semantic — it renames the *symbol*, not all matching
text. Safe across the project.

For local renames you can do it via the editor. For renames across
many files, use rename and then verify with `rg "old_name"` to catch
strings (LSP rename usually skips comments and string literals).

### Hover for learning

Hovering on an unfamiliar symbol shows its type and docstring. This is
your **primary learning interface** in a new codebase. Hover liberally.

### Inlay hints

Modern LSPs can show inferred types inline (e.g., Rust's `let x: i32 = …`
shown next to `let x = …`). Turn this on; it accelerates reading.

## When LSP Isn't Enough

LSP is symbol-aware but **single-language**. For:
- Cross-language refs → use cross-repo search (Sourcegraph).
- Reflection / dynamic dispatch → use grep.
- Historical code → use `git log -S`.
- Macro-expanded code → use language-specific tools (cargo-expand, etc.).

## See Also

- [search-tools.md](search-tools.md) — grep and friends, for when LSP can't see
- [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md) — installation and tuning
- [../09-unknown-tech/lsp-as-tutor.md](../09-unknown-tech/lsp-as-tutor.md) — using LSP to learn unfamiliar code
