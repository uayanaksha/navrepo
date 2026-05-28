# Python

Python's strength is approachability; its weakness is packaging and
environment management, which have a long, confusing history. Knowing
the *current* tooling and the classic gotchas gets you productive fast.

## Package & Environment Management

The landscape, current state:

| Tool | What it is | When you'll see it |
|---|---|---|
| **uv** | Fast, all-in-one (envs, installs, Python versions, lockfile); Rust-based | Increasingly the default in new projects |
| **pip** + **venv** | The standard-library baseline | Everywhere; the fallback |
| **Poetry** | Dependency management + packaging with lockfile | Many established projects |
| **pdm**, **Hatch** | Modern PEP-standard project managers | Some projects |
| **conda / mamba** | Cross-language envs incl. native deps | Data science, scientific stacks |

**uv** has rapidly become the recommended starting point — it's fast and
subsumes `pip`, `venv`, and `pyenv`'s roles:

```bash
uv venv                      # create a virtualenv
uv pip install -e ".[dev]"   # install (pip-compatible)
uv sync                      # install exactly from the lockfile
uv run pytest                # run a command in the project env
uv python install 3.12       # manage Python versions too
```

But **detect what the project uses** before reaching for your favorite:

```bash
ls uv.lock poetry.lock pdm.lock Pipfile.lock requirements*.txt
cat pyproject.toml           # [tool.poetry] / [tool.uv] / [build-system] tell you
```

Use the project's tool. A `poetry.lock` means `poetry install`; a
`uv.lock` means `uv sync`. Mixing tools corrupts environments.

## Virtualenvs: The Non-Negotiable

**Never install project dependencies into the system Python.** Always a
virtual environment, isolated per project:

```bash
python -m venv .venv
source .venv/bin/activate     # Windows: .venv\Scripts\activate
# ... now pip installs go into .venv, not your system
```

Most modern tools (uv, Poetry) create and manage the venv for you. The
cardinal rule remains: project deps live in an isolated env, not
globally. "It works in my other project" bugs are almost always env
contamination.

## Python Version Management

Projects pin a Python version; match it:

```bash
cat .python-version            # the pinned version
uv python install 3.12         # uv manages versions
# or pyenv install 3.12 / pyenv local 3.12
```

`uv`, `pyenv`, `mise`, and `asdf` all manage multiple Python versions.
Running the wrong Python version is a common source of "works for them,
not me" (see [../11-tooling/local-ci.md](../11-tooling/local-ci.md)).

## Testing

**pytest** is the de facto standard (even over the stdlib `unittest`):

```bash
pytest                         # run all tests
pytest tests/test_foo.py       # one file
pytest -k "name_substring"     # filter by test name
pytest -x                      # stop at first failure
pytest --lf                    # rerun last-failed only
pytest -q                      # quiet
```

Useful ecosystem: `pytest-cov` (coverage), `pytest-xdist` (`-n auto` for
parallel), `pytest-watch`/`ptw` (watch mode), fixtures for setup.

## Formatting & Linting

The modern answer is **Ruff** — a fast, Rust-based linter *and* formatter
that has largely consolidated the older stack (`black`, `flake8`,
`isort`, `pylint`):

```bash
ruff check .                   # lint
ruff check --fix .             # autofix
ruff format .                  # format (black-compatible)
```

You'll still see **black** (formatter) and **flake8**/**isort** in
established projects. Match the project's config (`pyproject.toml`,
`.ruff.toml`, `setup.cfg`).

## Type Checking

Python is gradually typed; many projects use type hints + a checker:

| Checker | Notes |
|---|---|
| **mypy** | The original; widely used |
| **pyright** | Fast, by Microsoft; powers Pylance in VS Code |
| **basedpyright** | A pyright fork with extra strictness/features |
| **ty**, **pyrefly** | Newer fast type checkers emerging |

```bash
mypy src/
pyright
```

Type hints are optional but, where present, the checker is part of CI —
run it before pushing. The LSP (Pylance/pyright) surfaces type errors as
you type (see [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)).

## Classic Gotchas

### Mutable default arguments

The single most famous Python trap:

```python
def f(items=[]):          # BUG: the list is created ONCE, shared across calls
    items.append(1)
    return items
f()  # [1]
f()  # [1, 1]  ← surprise; same list

def f(items=None):        # the fix
    if items is None:
        items = []
```

Default arguments are evaluated once at definition time, not per call.
Never use a mutable default; use `None` and create inside.

### The GIL

CPython's Global Interpreter Lock means threads don't run Python
bytecode truly in parallel — threading helps with I/O-bound work, not
CPU-bound. For CPU parallelism, use `multiprocessing` or native
extensions. (A free-threaded, no-GIL build is in progress as of recent
versions, but assume the GIL unless you know otherwise.)

### `is` vs `==`

`==` compares value; `is` compares identity. Use `==` for equality;
reserve `is` for `None`/singletons (`x is None`). Small-integer and
string interning makes `is` *sometimes* work by accident — don't rely on
it.

### Late-binding closures

```python
fns = [lambda: i for i in range(3)]
[f() for f in fns]            # [2, 2, 2], not [0, 1, 2]
# fix: capture per-iteration → lambda i=i: i
```

### Packaging confusion

Imports failing despite "installing" the package is usually env
contamination, an editable-install not done (`pip install -e .`), or a
`src/` layout not on the path. Check which env is active first.

### Others worth knowing

- **Integer/float division**, encoding issues, and `__pycache__` are
  rarely problems now but appear in old code.
- **`requirements.txt` without a lockfile** → non-reproducible installs;
  prefer a lockfile-based tool.

## Anti-Patterns

### Installing into system Python

Polluting the global interpreter and creating cross-project conflicts.
Always a venv.

### Fighting the project's tool

Running `pip install` in a Poetry project (or vice versa). Detect the
tool from the lockfile and use it.

### Ignoring the pinned Python version

Using whatever `python` is on your PATH instead of the project's pinned
version. Match `.python-version`.

### Mutable default arguments

The trap that bites everyone once. `None` sentinel, create inside.

## See Also

- [../09-unknown-tech/just-enough-learning.md](../09-unknown-tech/just-enough-learning.md)
- [../11-tooling/local-ci.md](../11-tooling/local-ci.md)
- [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)
- [javascript-typescript.md](javascript-typescript.md)
