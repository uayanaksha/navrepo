# JavaScript / TypeScript

The JS/TS ecosystem is the largest and fastest-moving — many package
managers, many bundlers, many ways to do everything. The skill is
*detecting* which the project uses and not fighting it.

## Package Managers

Four in common use; the lockfile tells you which:

| Manager | Lockfile | Notes |
|---|---|---|
| **npm** | `package-lock.json` | The default, ships with Node |
| **pnpm** | `pnpm-lock.yaml` | Fast, disk-efficient (content-addressed store); great for monorepos |
| **yarn** | `yarn.lock` | Classic (v1) and modern (Berry) differ a lot |
| **bun** | `bun.lockb` | Bun's manager (and runtime); very fast |

```bash
ls package-lock.json pnpm-lock.yaml yarn.lock bun.lockb
# then use the matching install:
npm ci          # clean install from lockfile (CI-style, reproducible)
pnpm install
yarn install
bun install
```

**`npm ci`** (not `npm install`) for reproducible installs from the
lockfile. Using the wrong manager regenerates a different lockfile and
causes churn — match the project. `corepack` can pin the exact manager
version a repo expects.

## Running Scripts

Commands live in `package.json`'s `scripts`:

```bash
cat package.json                 # read the "scripts" block first
npm run build                    # or pnpm/yarn/bun run
npm test
npm run dev
```

Always check `scripts` before guessing — `dev`, `build`, `test`, `lint`
are conventional but not guaranteed.

## TypeScript Config

`tsconfig.json` controls the compiler. The settings that matter most:

- **`strict`** — the big one. Strict mode enables null checks and much
  more; whether it's on changes how much the compiler protects you.
- **`target` / `module` / `moduleResolution`** — output JS version and
  module system; mismatches cause confusing import errors.
- **`paths`** — path aliases (`@/foo`); the editor and bundler must both
  understand them.

```bash
npx tsc --noEmit          # type-check without emitting JS (the CI check)
```

`tsc --noEmit` is the type-check; many projects run it separately from
bundling. The LSP gives you the same errors live (see
[../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)).

## Linting & Formatting

- **ESLint** — linter (bugs, style rules). Note the move to "flat config"
  (`eslint.config.js`) from the older `.eslintrc`.
- **Prettier** — formatter. Often paired with ESLint (ESLint for logic,
  Prettier for formatting).
- **Biome** — a newer fast Rust-based lint+format combo, gaining traction
  as an all-in-one.

```bash
npx eslint .
npx prettier --check .
npx biome check .
```

Match the project's config; run format-on-save (see
[../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)).

## Bundlers & Build Tools

You'll encounter several; know what each is for:

| Tool | Role |
|---|---|
| **Vite** | Dev server + build; the common modern default for apps |
| **esbuild** | Extremely fast bundler/transpiler (Vite uses it under the hood) |
| **webpack** | The established, highly-configurable bundler; lots of legacy |
| **Rollup** | Library bundling; Vite builds on it |
| **tsup**, **Rolldown**, **Turbopack** | Newer/library-focused entrants |
| **swc** | Fast Rust transpiler (Babel alternative) |

You rarely configure these from scratch — you inherit a config. Identify
which one, read its config file, and use the project's `dev`/`build`
scripts.

## Testing

| Runner | Notes |
|---|---|
| **Vitest** | Vite-native, fast, Jest-compatible API; common in new projects |
| **Jest** | The long-standing standard; huge ecosystem |
| **node:test** | Built into Node now; no dependency |
| **Playwright / Cypress** | End-to-end browser testing |

```bash
npx vitest          # or: npx jest, node --test
npx vitest --watch
```

## Monorepos

JS/TS pioneered much monorepo tooling (see
[../14-advanced/working-in-monorepos.md](../14-advanced/working-in-monorepos.md)):

- **pnpm workspaces** — `pnpm-workspace.yaml`; efficient and popular.
- **npm/yarn workspaces** — the `workspaces` field in `package.json`.
- **Turborepo** — task pipeline + caching on top of a workspace.
- **Nx** — fuller-featured task graph, affected-detection, generators.

Find the workspace config; use the affected/scoped commands rather than
building everything.

## Node Version

Projects pin a Node version (`.nvmrc`, `engines` in `package.json`):

```bash
cat .nvmrc
nvm use            # or fnm use / volta / mise
```

Mismatched Node versions cause native-module and syntax errors. Match
the pin.

## Classic Gotchas

### `this` binding

`this` depends on *how a function is called*, not where it's defined.
The classic break:

```js
class C {
  val = 1;
  method() { return this.val; }
}
const m = new C().method;
m();                    // undefined `this` — `this` is lost when detached
// fixes: arrow methods, .bind(this), or call as obj.method()
```

Arrow functions capture `this` lexically; regular functions don't. This
trips up event handlers and callbacks constantly.

### Equality: `==` vs `===`

`==` does type coercion with famously surprising results (`0 == ""`,
`null == undefined`, `[] == false`). **Always use `===`/`!==`** unless
you deliberately want coercion. Linters enforce this.

### Async pitfalls

- **Unhandled promise rejections** — a missing `await` or `.catch()`
  swallows errors. Always handle rejections.
- **Forgetting `await`** — you get a `Promise`, not the value, and the
  bug is silent.
- **`forEach` with async** doesn't await — use `for...of` with `await`,
  or `Promise.all(map(...))`.
- **Floating promises** — fire-and-forget async that errors invisibly.

### Other well-known traps

- **`NaN !== NaN`** — use `Number.isNaN()`.
- **Mutating shared objects/arrays** passed by reference.
- **`null` vs `undefined`** — two empties; `strictNullChecks` helps.
- **Floating-point** (`0.1 + 0.2 !== 0.3`) — universal, not JS-specific.
- **CommonJS vs ESM** (`require` vs `import`) interop is a frequent
  source of confusing module errors.

## Anti-Patterns

### Wrong package manager

Running `npm install` in a pnpm repo, regenerating a conflicting
lockfile. Match the lockfile; use `corepack` if pinned.

### `npm install` instead of `npm ci` in CI

Non-reproducible installs that drift from the lockfile. Use `ci`.

### Fighting the inherited bundler config

Trying to swap webpack for Vite mid-project on a whim. Inherit and use
the existing config unless there's a real reason.

### `==` and lost `this`

The two gotchas that bite everyone. `===` always; mind how functions are
called.

### Ignoring the Node version pin

Native-module and syntax errors from a mismatched Node. Match `.nvmrc`.

## See Also

- [../14-advanced/working-in-monorepos.md](../14-advanced/working-in-monorepos.md)
- [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)
- [python.md](python.md)
- [../09-unknown-tech/lang-vs-codebase-confusion.md](../09-unknown-tech/lang-vs-codebase-confusion.md)
