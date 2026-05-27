# LSP as Tutor

A well-configured Language Server teaches you a language by hover,
autocomplete, and inline errors. Get this working before any
tutorials.

## What LSP Tells You

### Hover

Hover over any symbol → type, signature, docstring.

```rust
fn process(items: Vec<Item>) -> Result<Stats, ProcessError> {
//   ^^^^^^^                            ^^^^^^^^^^^^^^^^^^
//   hover shows the full signature     hover shows the type
}
```

In an unknown language, hover liberally. The LSP is teaching you the
type system through real code.

### Autocomplete

Start typing:

```python
order.|
       autocomplete shows: id, items, total, place(), cancel()
```

Tells you what's on the object without reading the class.

### Inline diagnostics

```rust
let x: i32 = "5";
//           ^^^ expected i32, found &str
```

The LSP catches type errors before you run. Reading those errors
teaches you what the type checker cares about.

### Inlay hints

```rust
let result = some_function();
//  ^^^^^^^/* Result<Vec<User>, Error>*/
```

Shows inferred types inline. Free reading aid in inferred languages.

Turn this on in your editor. Hugely useful in Rust, Go (especially),
TypeScript.

### Go-to-definition / find-references

In a new codebase, these are how you walk the graph. See
[../02-navigation/lsp-navigation.md](../02-navigation/lsp-navigation.md).

## Workflow in an Unknown Language

1. Open the project; LSP starts.
2. Wait for it to index.
3. Open the file you'll work in.
4. Hover everything to absorb types.
5. Read with go-to-definition for unfamiliar symbols.
6. Start typing; let autocomplete teach you the API.
7. Save → format-on-save fixes your syntax.
8. Lints / diagnostics tell you what's wrong.

That's a tight learning loop. You don't need a book.

## When LSP Doesn't Work

### Server isn't installed

Each language has its own server (`gopls`, `rust-analyzer`, `pyright`).
Install before opening the file.

### Project doesn't build

LSP often needs the project to compile to be useful. If your build is
broken, fix that first.

### Files outside the project

If you opened a single file, the LSP doesn't have the project context.
Open the project root.

### Slow indexing

Large projects take minutes to index. Wait. Drink coffee.

### Macros / generated code / FFI

LSP often can't see through macros (Rust), generated code (protobuf
stubs), or FFI. You'll need to fall back to reading the actual source.

## Hover Hygiene

A few useful behaviors:

### Hover when stuck

If you're confused, hover. Often the type tells you what's happening.

### Hover the imports

When opening a file, hover its imports. Tells you what libraries are
in use.

### Hover the parameters

Function parameters' types tell you the contract.

### Hover the inferred types

In Rust / Go / TypeScript: hover any `let` or `:=` to see what the
compiler inferred.

## Snippets and Boilerplate

Many language servers offer snippets:

- Type `match`, get a match expression scaffold.
- Type `fn`, get a function signature template.
- Type `tdd`, sometimes get a test stub.

Use these for boilerplate. Don't memorize syntax that snippets can
type for you.

## Code Actions in Unknown Tech

Code actions (Ctrl+. / Quick Fix) often suggest:

- Auto-import.
- Generate missing implementations.
- Extract / inline.
- Fix specific lints.

In a new language, explore these. They show you what the language
considers "normal operations."

## Linter Output Is Educational

Run the linter. Read the warnings:

- Rust: `clippy` lints often teach idiom.
- Go: `staticcheck`, `golangci-lint`.
- Python: `ruff`, `pylint`.
- TypeScript: `eslint`.
- Many others.

Each lint explains a best practice with example. You're getting a
free tutorial.

## Pairing LSP with Documentation

When hover docs aren't enough:

1. See the symbol type via hover.
2. Look up the type in official docs.
3. Read its methods, related types.
4. Come back to your code with the broader context.

The LSP gives you the entry point; docs give you the depth.

## Treat LSP Failures as Information

If LSP can't find a definition or shows an error you didn't expect:

- The project might not be building.
- The symbol might come from generated code.
- There might be a config issue.

Investigating these often teaches you something about the project's
structure — generated code lives over there, this is a workspace
member, etc.

## Common LSP Setup Mistakes

### Two LSP plugins fighting

Especially with Python (Pyright vs Pylsp vs others). Pick one.

### Stale cache

If LSP shows weird results, restart the server:

- VS Code: "Reload Window."
- Neovim: `:LspRestart`.
- Helix: kill server process.

### Wrong root detection

If LSP attaches to the wrong root, it can't find dependencies. Make
sure you opened the project root, not a subdirectory.

## LSP for Reading vs Writing

When **reading** unfamiliar code:
- LSP shows types (the "what is this?" question).
- Find-references shows usage (the "how is this used?" question).
- Go-to-def shows source (the "what does this do?" question).

When **writing** unfamiliar code:
- Autocomplete proposes shapes (the "what can I do here?" question).
- Inline errors catch mistakes (the "is this right?" question).
- Code actions automate boilerplate (the "how do I write this?" question).

Both are first-class.

## See Also

- [../02-navigation/lsp-navigation.md](../02-navigation/lsp-navigation.md)
- [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)
- [just-enough-learning.md](just-enough-learning.md)
