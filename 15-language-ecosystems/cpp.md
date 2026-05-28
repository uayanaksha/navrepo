# C++

C++ gives you maximum control and maximum ways to shoot yourself. The
ecosystem has no single blessed build system or package manager, so
*every* project's setup differs — and the language's undefined behavior
makes the sanitizers (see
[../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md))
essential, not optional.

## Build Systems

There's no one standard; identify what the project uses:

| System | Files | Notes |
|---|---|---|
| **CMake** | `CMakeLists.txt` | The de facto common denominator; generates native builds |
| **Bazel** | `BUILD`, `WORKSPACE` | Hermetic, scalable; large/Google-style projects |
| **Meson** | `meson.build` | Modern, fast, clean syntax; growing |
| **Make** | `Makefile` | Classic, manual; older/smaller projects |
| **Autotools** | `configure`, `Makefile.am` | Legacy Unix (`./configure && make`) |

CMake is what you'll meet most:

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug    # configure
cmake --build build -j                          # build (parallel)
ctest --test-dir build                          # run tests
# Tip: -DCMAKE_EXPORT_COMPILE_COMMANDS=ON generates compile_commands.json
```

**`compile_commands.json`** (the "compilation database") is what your
LSP (`clangd`) needs to understand the project — generate it. Without it,
editor navigation in C++ is badly degraded.

## Compiler Choice

Unlike most languages, the *compiler* is a variable:

- **GCC** (`g++`) and **Clang** (`clang++`) are the mainstream;
  **MSVC** (`cl`) on Windows.
- They differ in warnings, diagnostics, supported standard features, and
  occasionally behavior. Code that builds on one may warn or fail on
  another.
- **Clang** generally has friendlier diagnostics and powers the tooling
  (clangd, clang-tidy, clang-format, the sanitizers).
- Match the **C++ standard** the project targets (`-std=c++17`,
  `c++20`, `c++23`) — features and behavior depend on it.

## Package Management

Historically C++'s weakest area; there's now real tooling, but no single
winner:

| Manager | Notes |
|---|---|
| **vcpkg** | Microsoft's; large catalog; integrates with CMake |
| **Conan** | Decentralized, flexible; popular in industry |
| **system packages** | `apt`/`brew`/etc. — many projects just expect libs installed |
| **vendored / submodules** | Dependencies checked in or as git submodules |

Detect the approach (`vcpkg.json`, `conanfile.txt`/`.py`, git submodules,
or a README "install these libs"). There's no `cargo`/`npm` universality
here.

## Formatting & Linting

```bash
clang-format -i file.cpp        # format (project ships a .clang-format)
clang-tidy file.cpp             # lint / static analysis
cppcheck .                      # additional static analysis
```

`clang-format` with the project's `.clang-format` is standard; run it
before pushing. `clang-tidy` catches bugs and modernization
opportunities.

## Sanitizers Are Essential

C++ has **undefined behavior** — code that's invalid but may *appear* to
work, until it doesn't. The sanitizers (compiler instrumentation) are
your primary defense and should run in CI:

```bash
g++ -g -fsanitize=address,undefined prog.cpp -o prog && ./prog   # ASan + UBSan
g++ -g -fsanitize=thread prog.cpp -o prog && ./prog              # TSan (data races)
```

| Sanitizer | Catches |
|---|---|
| **ASan** | Use-after-free, buffer overflow, leaks |
| **UBSan** | Undefined behavior (overflow, bad shifts, null deref) |
| **TSan** | Data races |
| **MSan** | Uninitialized memory reads |

A C++ test suite that's green *without* sanitizers can be full of UB.
Run ASan+UBSan at minimum. **Valgrind** is the no-recompile alternative
(slower). See [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md).

## Classic Gotchas

### Undefined behavior (UB)

The defining C++ hazard. UB means the standard imposes *no* requirements
— the compiler may do anything, including "work" in debug and break under
optimization. Common sources:

- Signed integer overflow, out-of-bounds access, use-after-free.
- Reading uninitialized memory, null/dangling pointer deref.
- Data races, violating strict aliasing.

UB is why "it worked yesterday / in debug / on my machine" is so common
in C++. The compiler *assumes UB never happens* and optimizes
accordingly, so UB bugs are often invisible until an optimization level
or compiler changes. Sanitizers exist precisely to catch UB.

### Lifetime / dangling references

Returning a reference or pointer to something that's destroyed:

```cpp
const std::string& f() {
    std::string s = "local";
    return s;            // BUG: dangling reference — s is destroyed on return
}
```

Manual lifetime management is the deepest source of C++ bugs. Modern C++
mitigates with **RAII** and **smart pointers** (`std::unique_ptr`,
`std::shared_ptr`) — prefer them over raw `new`/`delete`. ASan catches
many lifetime errors at runtime.

### The ODR (One Definition Rule)

A symbol may be *defined* once across the whole program. Violations —
inconsistent definitions in different translation units, a non-inline
function defined in a header included twice — cause confusing link errors
or silent UB. Header/inline discipline matters.

### Build / linker errors

C++'s separate compilation produces a famously cryptic error class:

- **Linker errors** (`undefined reference`) — declared but not defined,
  or not linked. Check what's compiled and linked.
- **Template errors** — historically enormous and unreadable (improving
  with concepts in C++20).
- **Header include issues** — missing includes, include order, the
  preprocessor's textual nature.

### Other gotchas

- **Manual memory** — leaks, double-frees; use RAII/smart pointers.
- **Integer/implicit conversions** — narrowing, signed/unsigned
  comparison surprises.
- **Copy vs move** — accidental expensive copies; understand move
  semantics.
- **`#include` is textual** — the preprocessor pastes files; slow builds
  and order-dependence (modules in C++20 aim to fix this).

## Anti-Patterns

### No sanitizers

Trusting a green suite that never ran under ASan/UBSan in a language
riddled with UB. Run sanitizers in CI.

### Raw `new`/`delete` everywhere

Manual memory management when RAII and smart pointers would prevent leaks
and use-after-free. Prefer smart pointers.

### No compile_commands.json

Suffering broken editor navigation because clangd has no compilation
database. Generate it (`CMAKE_EXPORT_COMPILE_COMMANDS=ON`).

### Assuming one compiler == all compilers

Code that builds clean on GCC failing on Clang/MSVC. Test on the
project's target compiler(s) and standard.

### Treating UB as "works fine"

Relying on undefined behavior that happens to work at the current
optimization level. It will break under a new compiler/flag. Fix the UB.

## See Also

- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)
- [../14-advanced/debugging-toolkit-deep-dive.md](../14-advanced/debugging-toolkit-deep-dive.md)
- [rust.md](rust.md)
- [multi-language-monorepos.md](multi-language-monorepos.md)
