# Search Tools

Code search is the most common operation in software engineering. Investing
in your search skills pays back daily.

## Hierarchy

Pick the **highest-level** tool that answers your question:

1. **LSP** — for symbols (see [lsp-navigation.md](lsp-navigation.md)).
2. **ripgrep (`rg`)** — for text.
3. **Structural search (`ast-grep`, `comby`)** — for syntactic patterns.
4. **Git pickaxe (`git log -S/-G`)** — for code that *existed* once.
5. **Cross-repo (Sourcegraph, GitHub code search, grep.app)** — for usage
   patterns across projects.

## ripgrep (`rg`)

Default tool for "where is this string?"

```bash
# basics
rg "needle"                          # search current dir, respecting .gitignore
rg "needle" path/                    # limit to a path
rg -t go "needle"                    # only .go files
rg -T test "needle"                  # exclude tests
rg -F "literal.string"               # fixed string, no regex
rg -i "needle"                       # case-insensitive
rg -w "word"                         # whole word
rg "needle" -l                       # only filenames
rg "needle" -c                       # count per file
rg --files | rg "test_.*\.py"        # list files, then grep names
rg "needle" -A 5 -B 2                # 5 lines after, 2 before context
rg "needle" --no-heading             # plain output, easy to pipe
```

Common patterns:

```bash
# Find all error messages a user might see
rg 'errors?\.New\("[^"]+"\)' --type go

# Find all TODO/FIXME with file:line
rg '(TODO|FIXME|XXX)' --vimgrep

# Find any function called `process_*` in Python
rg 'def process_\w+\(' --type py

# Show me only the unique error strings
rg -o 'errors\.New\("[^"]+"\)' | sort -u
```

### Performance notes

`rg` is faster than `grep`/`ag` because it:
- Respects `.gitignore` by default.
- Uses parallel directory walking.
- Uses SIMD-accelerated regex.

For huge repos, `rg -t go` (file-type filter) is dramatically faster
than `rg "needle" *.go`.

## ripgrep alternatives

| Tool | Use when |
|---|---|
| `rg` | Default for almost everything |
| `grep` | On servers without `rg`; standard tooling |
| `git grep` | Search only tracked files; respects git's view |
| `ag` (silver-searcher) | Older but still around; mostly `rg` is better |
| `ack` | Perl-based; legacy; `rg` is better |

## Structural Search

`rg` finds text. Structural tools find *patterns* — function calls with a
particular shape, regardless of variable names.

### ast-grep

```bash
# Find all calls to .unwrap() in Rust code
ast-grep -p '$$$.unwrap()' --lang rust

# Find all if/else where both branches return
ast-grep -p 'if $C { return $X; } else { return $Y; }' --lang rust

# Find unused parameters in JS
ast-grep -p 'function $F($A, $B, $C) { /* $A unused */ }'
```

### comby

```bash
# Replace all println! with log::info!
comby 'println!(:[args])' 'log::info!(:[args])' .rs

# Find all function calls with three args where the second is `nil`
comby 'foo(:[a], nil, :[c])' '' .go
```

When to use structural search:
- The pattern is syntactic, not textual (e.g., "all `.unwrap()` calls").
- You're doing a large mechanical refactor.
- You need to match across whitespace/formatting variations.

When NOT:
- For one-off finds — `rg` is faster to type.
- When you don't yet have a precise pattern in mind.

## Git Pickaxe — Search History

`rg` searches the current working tree. **`git log -S` searches all of
history.** Critical when:

- Code was deleted and you want to know why.
- A symbol exists in old versions but not current.
- You want to find when something *first* appeared.

```bash
# When did "needle" first or last appear?
git log -S "needle" --all --source --pretty=oneline

# Same, but with diffs
git log -S "needle" -p --all

# Regex version (less efficient)
git log -G "regex.*pattern" --all

# When did a specific function exist?
git log -S "func ProcessRequest" --all --pretty=format:'%h %ad %s' --date=short

# Show commits that touched a file (including renames)
git log --follow --oneline path/to/file.go
```

Use `-S` (pickaxe) when you can name a specific string. Use `-G` (regex)
when you need a pattern. `-S` is much faster.

## GitHub Code Search

The web UI (and `gh search`) lets you search across all of GitHub:

```bash
gh search code "func ProcessRequest" --language=go --repo=owner/repo
gh search code "function processRequest" --language=javascript
```

Use cases:
- "How do other projects use this library function?"
- "Is this a common pattern, or did this team invent it?"
- "What does this API look like in real-world usage?"

The web UI supports syntax like `language:rust /pattern/` with regex.

## Sourcegraph

Free for public code; many enterprises self-host. Stronger than GitHub
for:

- Find references across multiple repos.
- Structural search across organizations.
- Big-result queries that GitHub truncates.

### grep.app

A faster, simpler alternative for cross-public-repo grep. Useful for a
quick "is this idiom common?"

## Choosing a Tool: Practical Examples

**"Where is `process_payment` called?"**
→ LSP find-references first. Fall back to `rg 'process_payment\('`.

**"Why does this comment mention `LegacyAuthMiddleware` that doesn't exist?"**
→ `git log -S "LegacyAuthMiddleware" --all` to find when it was removed.

**"Find all instances of `.unwrap()` in Rust code so I can review them."**
→ `ast-grep -p '$$$.unwrap()' --lang rust`.

**"How do other open-source projects use the `tokio::select!` macro?"**
→ GitHub code search or grep.app.

**"What's every place we hardcoded a URL?"**
→ `rg 'https?://[^"\s]+'`.

**"All test files that mock the database?"**
→ `rg -l 'MockDB|mock_db' --type=test` (or similar based on convention).

## Speed Tips

- Pipe `rg --files` through `fzf` for interactive file selection.
- Bind a hotkey in your editor to "search project for word under cursor."
- Save common searches as shell aliases or scripts.
- For repeated narrow searches, use `rg --files-with-matches` first to
  see *which files match*, then drill in.

## Anti-Patterns

- **Using grep/rg when LSP would do it better.** LSP knows scope; grep doesn't.
- **Manually `find | xargs grep`.** `rg` is faster and respects `.gitignore`.
- **Searching for a UI string and ignoring i18n.** Strings come from
  translation files; search those too.
- **Forgetting case sensitivity.** Match what you're looking for; use `-i`
  if you're not sure.

## See Also

- [lsp-navigation.md](lsp-navigation.md) — the symbol-aware companion to text search
- [git-archaeology.md](git-archaeology.md) — deeper git-as-search techniques
- [../11-tooling/shell-and-cli.md](../11-tooling/shell-and-cli.md) — installing these tools
