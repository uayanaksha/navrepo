# Editor and LSP

The editor is the tool you spend most of your day in. Whatever you
use, invest in it.

## Editor Choices

The honest landscape (2026):

| Editor | Strengths |
|---|---|
| VS Code | Most ecosystems supported; biggest extension marketplace; AI integration |
| Neovim | Highly customizable; fast; LSP via plugins |
| JetBrains IDEs | Best-in-class for Java / Kotlin / Python / Go; opinionated |
| Helix | Modal, modern, batteries-included; smaller ecosystem |
| Emacs | Most extensible; smaller community |
| Zed | Modern; collaborative; fast |
| Sublime Text | Fast; minimalist; smaller market share |

There's no "best." There's "the one you'll invest in." Pick one and
go deep.

## What to Configure

For any editor:

### 1. LSP

For every language you use:
- Install the language server.
- Verify go-to-def works.
- Enable inlay hints.
- Set up format-on-save.

See [../02-navigation/lsp-navigation.md](../02-navigation/lsp-navigation.md).

### 2. Linters / formatters

- Run linter inline (warnings as you type).
- Apply formatter on save.
- Match the project's config.

Project-specific: `.editorconfig`, `.prettierrc`, `pyproject.toml`,
etc. should be auto-detected.

### 3. Keybindings

The 10 most useful, learned cold:

| Action | Save you... |
|---|---|
| Go to definition | Walking to where a function is defined |
| Find references | Knowing who calls something |
| Workspace symbol search | Finding any symbol |
| Quick fix / code action | Fixing the warning |
| Rename symbol | Renaming everywhere safely |
| Multi-cursor | Editing parallel sites |
| Comment line / block | The most common edit |
| Search in project | grep replacement |
| Open file by name | Find that file fast |
| Hover for type info | Cheap learning |

Whatever editor: learn these keystrokes.

### 4. Terminal integration

A built-in terminal in your editor saves a context switch. Or set up
a tiled window manager / tmux for fluid switching.

### 5. Debugger

For each language you debug, configure the debugger. Set a breakpoint,
hit it, step. Verify it works.

You'll thank yourself the first time you can avoid 30 minutes of
print-debugging.

## VS Code Specifics

If using VS Code:

- **Extensions to install** per language: official ones first.
- **Settings sync**: turn on; sync across machines.
- **Keybindings**: copy from somewhere you like (Vim / Emacs / etc.)
  or learn defaults.
- **Workspace settings**: per-project settings in `.vscode/settings.json`.
- **Tasks and launch configs**: encode common commands.
- **Profile per language / mode**: light setup for prose vs heavy for code.

## Neovim Specifics

If using Neovim:

- **Distribution choice**: `LazyVim`, `AstroNvim`, `NvChad`, or
  hand-rolled. Don't start from zero unless you want to spend weeks.
- **LSP via `nvim-lspconfig` + `mason.nvim`** for server management.
- **Completion** via `nvim-cmp` or `blink.cmp`.
- **Telescope or `fzf-lua`** for fuzzy finding.
- **Treesitter** for syntax highlighting and structural editing.
- **Statusline**: lualine or similar.
- **Git**: gitsigns, neogit, fugitive.

Don't tweak forever. Spend a week setting up, then commit to working.

## JetBrains Specifics

If using a JetBrains IDE:

- **Per-language IDEs** (IntelliJ IDEA for Java, GoLand for Go, etc.)
  or IntelliJ + plugins.
- **Inspections** are powerful — they're effectively a more
  sophisticated LSP.
- **Refactoring tools** are best-in-class.
- **Plugins**: keep it minimal; the core is already heavy.
- **Settings**: sync via JetBrains account.

## Format-on-Save

The single most impactful setting. Saves:

- Manual indent fixing.
- Linter-required whitespace adjustments.
- Inconsistent commits.

Just on, everywhere. The format gets applied; you stop thinking about
it.

## Per-Project Configuration

Many projects ship editor config:

- `.editorconfig` — universal across editors.
- `.vscode/` — VS Code settings, debug configs.
- `.idea/` — JetBrains.
- `.helix/` — Helix.
- `.nvim.lua` (project-local Neovim).

**Respect** the project's config when committed. Don't override.

## Editor Performance

When your editor gets slow:

- Disable extensions you don't need.
- Profile (most editors have a "show profile" command).
- Watch memory for runaway language servers.
- Reduce indexing scope for monorepos.

Slow editor = slow you.

## Editor as a Career Investment

Whatever editor you choose, you'll likely use it for years. Investment
in it is a force multiplier:

- Hours learning keybindings.
- Days learning the customization model.
- Months learning to extend it.

Some pay-offs:
- Write a snippet → save hundreds of similar typings.
- Write a script → automate a routine task.
- Share config → improve teammates' setups.

## The Debugger

Most developers under-use debuggers because setup is annoying. Fix
once; benefit forever.

Per-language:
- **Python**: built-in `pdb`, or `breakpoint()`.
- **Node**: `--inspect`, Chrome DevTools.
- **Go**: `dlv`, integrated with VS Code/Goland.
- **Rust**: `lldb`, `rust-lldb`. Or `cargo flamegraph` for perf.
- **Java/Kotlin**: JetBrains' debugger.
- **C/C++**: `gdb`, `lldb`. Or `rr` for record-and-replay (Linux).

For each: learn how to set a breakpoint, step, inspect variables.
30 minutes; lasting value.

## When to Switch Editors

Reasons to switch:

- The new editor has dramatically better support for what you do.
- Your current editor is dead / unmaintained.
- You'll be using a new language where the old editor is weak.

Reasons not to switch:

- Hype.
- "Mine is faster" benchmarks.
- New shiny.

Switching costs weeks of productivity. Make the new editor pay off.

## See Also

- [shell-and-cli.md](shell-and-cli.md)
- [debugging-tools.md](debugging-tools.md)
- [../02-navigation/lsp-navigation.md](../02-navigation/lsp-navigation.md)
