# Shell and CLI

The terminal is where you spend the rest of your day. A few tools and
config tweaks make it dramatically more useful.

## The Essential Toolkit

| Tool | Replaces / improves | Notes |
|---|---|---|
| **`ripgrep` / `rg`** | grep | Fast, gitignore-aware |
| **`fd`** | find | Sane defaults, fast |
| **`fzf`** | manual selection | Fuzzy finder; pairs with everything |
| **`bat`** | cat | Syntax highlighting; piping |
| **`delta`** | git diff | Side-by-side, syntax-highlighted diffs |
| **`gh`** | (GitHub web UI) | Issues, PRs, CI from terminal |
| **`jq`** | manual JSON parsing | JSON CLI |
| **`yq`** | manual YAML parsing | YAML CLI (jq-compatible) |
| **`zoxide`** / **`z`** | cd | Frecency-based jumps |
| **`tldr`** | man pages | Quick reference for commands |
| **`htop`** / **`btop`** | top | Interactive process viewer |
| **`atuin`** | bash history | Searchable, synced history |
| **`tmux`** | window management | Persistent sessions |
| **`hyperfine`** | manual timing | Benchmarking CLIs |
| **`watchexec`** | manual rerun | Run command on file change |

## Installation

Most are available via:

- **macOS**: `brew install ripgrep fd fzf bat git-delta gh jq yq zoxide tldr htop`
- **Linux (apt)**: `apt install ripgrep fd-find fzf bat ...` (names may vary)
- **Linux (others)**: similar, check your package manager.

Cargo (`cargo install ...`) works for Rust-based ones.

Many ship as static binaries you can install per-user.

## Configure fzf

`fzf` shines with shell integration. After install:

```bash
$(brew --prefix)/opt/fzf/install
# or
~/.fzf/install
```

This adds:

- **Ctrl-R**: fuzzy search shell history.
- **Ctrl-T**: fuzzy file picker.
- **Alt-C**: fuzzy directory jump.

Once you have these, you'll wonder how you lived without.

Pair `fzf` with `rg`:

```bash
# In your shell rc:
export FZF_DEFAULT_COMMAND='rg --files --hidden --follow --glob "!.git"'
```

## Configure ripgrep

`~/.ripgreprc` for defaults:

```
--smart-case
--hidden
--glob=!.git/*
--glob=!node_modules/*
--glob=!target/*
```

Set:

```bash
export RIPGREP_CONFIG_PATH="$HOME/.ripgreprc"
```

## Configure zoxide

```bash
# in your shell rc
eval "$(zoxide init bash)"   # or zsh, fish
```

Then `z partial_directory_name` jumps to frequently-visited dirs.

## Configure delta

`~/.gitconfig`:

```ini
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true
    light = false       # or true
    line-numbers = true

[merge]
    conflictstyle = zdiff3
```

`git diff`, `git show`, `git log -p` now look great.

## tmux

For persistent sessions and pane management:

```bash
tmux new -s work        # new named session
tmux attach -t work     # reattach
tmux ls                 # list sessions
```

Inside tmux:
- **Prefix + |**: split vertically (with config).
- **Prefix + -**: split horizontally (with config).
- **Prefix + d**: detach.
- **Prefix + n / p**: next / previous window.

Sample `~/.tmux.conf`:

```
set -g prefix C-a
unbind C-b
bind C-a send-prefix
bind | split-window -h
bind - split-window -v
set -g mouse on
set -g history-limit 50000
```

## Shell Choice

| Shell | Pros |
|---|---|
| **bash** | Universal; lowest setup |
| **zsh** | Better completion; many plugins |
| **fish** | Friendly out of the box; less POSIX |
| **nushell** | Structured data; modern |

Most scripts are bash; most interactive shells are zsh or fish.
Don't agonize over the choice.

## Shell Frameworks

Optional speed-ups for zsh:

- **Oh My Zsh**: classic; many themes / plugins.
- **Prezto**: lighter alternative.
- **Zinit / zplug**: plugin managers.
- **Starship**: cross-shell prompt.

Don't overdo plugins. A heavy prompt slows your shell.

## Aliases You'll Use Daily

```bash
# ~/.bashrc or ~/.zshrc
alias ll='ls -lah'
alias gs='git status -sb'
alias gd='git diff'
alias gl='git log --oneline --graph --decorate -20'
alias gco='git checkout'
alias gcb='git checkout -b'
alias gp='git pull'
alias gpf='git push --force-with-lease'

# venv helpers (Python)
alias venv='python -m venv .venv && source .venv/bin/activate'
alias activate='source .venv/bin/activate'

# common dirs
alias dev='cd ~/dev'
alias projects='cd ~/projects'
```

Build your own based on what you type often.

## Functions

For more complex shortcuts:

```bash
# fkill: kill a process by fuzzy search
fkill() {
    local pid
    pid=$(ps -ef | sed 1d | fzf -m | awk '{print $2}')
    [ "$pid" != "" ] && kill -${1:-9} $pid
}

# fbr: fuzzy branch checkout
fbr() {
    local branches branch
    branches=$(git branch -a --no-color | grep -v HEAD) &&
    branch=$(echo "$branches" | fzf -d $(( 2 + $(wc -l <<< "$branches") )) +m) &&
    git checkout $(echo "$branch" | sed "s/.* //" | sed "s#remotes/[^/]*/##")
}
```

## Cross-Machine Sync

If you use multiple machines:

- **`chezmoi`** or **`yadm`**: dotfile management.
- **GNU stow**: simpler dotfile symlinking.
- **`atuin`**: synced shell history across machines.
- **VS Code / JetBrains settings sync**: built-in.

Set up once; thank yourself across years.

## CLI for GitHub: `gh`

```bash
gh issue list
gh issue view 123
gh issue create

gh pr list
gh pr view 1234 --comments
gh pr diff 1234
gh pr checkout 1234
gh pr create --draft --title "..." --body "..."
gh pr merge --auto --squash

gh run list                    # CI runs
gh run watch                   # watch latest run
gh run rerun <id>

gh repo clone owner/repo
gh repo fork owner/repo
gh repo view --web

gh search code "..." --language=go
```

Worth memorizing. Avoids context switches to the browser.

## CLI for Cloud: provider-specific

- **AWS**: `aws`, `aws-vault`.
- **GCP**: `gcloud`.
- **Azure**: `az`.
- **k8s**: `kubectl`, `kubectx`/`kubens`, `k9s`.

Each has its own learning curve. Match what your work needs.

## Watch / Run on Change

```bash
# Re-run tests when files change
watchexec -e py -- pytest tests/test_foo.py
watchexec -e rs -- cargo test
watchexec -e go -- go test ./...
```

Tight feedback loop.

Alternatives:
- `entr` (similar tool).
- Language-specific watchers (`cargo watch`, `nodemon`).

## Performance Measurement

```bash
hyperfine './script-a.sh' './script-b.sh'
# Compares means and standard deviations across runs.
```

Better than `time` for comparing alternatives.

## Anti-Patterns

### Memorizing every flag

Learn the 5–10 most useful per tool. Use `tldr`, `--help`, man pages
for the rest.

### Heavy prompts

A prompt that takes 500ms to render makes the shell feel sluggish.
Trim what you actually need to see.

### One huge `.bashrc`

Sourceable submodules:

```bash
# ~/.bashrc
for f in ~/.bashrc.d/*.sh; do source "$f"; done
```

`~/.bashrc.d/aliases.sh`, `~/.bashrc.d/functions.sh`, etc. Easier to
maintain.

### Auto-updating tools breaking your setup

Pin versions if you care about stability. `brew pin <pkg>` etc.

## See Also

- [editor-and-lsp.md](editor-and-lsp.md)
- [git-config.md](git-config.md)
- [../02-navigation/search-tools.md](../02-navigation/search-tools.md)
