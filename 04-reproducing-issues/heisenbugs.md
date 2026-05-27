# Heisenbugs

A Heisenbug changes behavior when you observe it. Add a print
statement and it disappears. Run under the debugger and it never
triggers. Run in production at 3am and it's everywhere.

The name is half-joke, but the phenomenon is real and important. A
Heisenbug is a signal — it tells you what *kind* of bug you have.

## What Causes Heisenbugs

### Race conditions

Two threads / goroutines / async tasks accessing shared state without
proper synchronization. The bug depends on exact timing.

Adding `print()` changes timing → bug disappears (or appears in a new
spot).

### Optimizer dependence

In release builds, the compiler can:
- Reorder reads/writes.
- Eliminate "unused" code that had side effects you missed.
- Inline functions, changing stack layout.

Adding logging changes inlining → bug behavior changes.

### Uninitialized memory

Reading uninitialized memory returns whatever was there. Different
runs, different layouts, different "garbage" values.

In C/C++, common. In safe languages, rare but possible via FFI or
`unsafe`.

### TOCTOU (time-of-check / time-of-use)

```python
if os.path.exists(path):
    open(path)  # someone deleted it between check and open
```

The bug requires another process to interleave. Reproducing on a
single-user dev machine is rare.

### Heap fragmentation / GC timing

Long-running processes may behave differently after hours of work
than they do at startup. Hard to repro in a short test run.

### Network jitter / OS scheduling

The bug requires a specific delay or scheduler decision. Hard to
control.

## Diagnosing Heisenbugs

### Step 1 — Suspect timing

If you've seen these signs, you have a Heisenbug:

- "Sometimes" fails.
- More likely under load.
- More likely on slower / faster machines.
- Disappears with logging.
- Disappears under debugger.
- Test passes alone but fails in suite.
- Failure rate varies by run count.

### Step 2 — Classify

| Symptom | Likely cause |
|---|---|
| Multi-threaded code; weird values | Race condition |
| Optimizer-dependent (release ≠ debug) | UB or compiler reordering |
| Random garbage values | Uninitialized memory |
| Filesystem / network operations | TOCTOU or external timing |
| Memory issues after many requests | GC, fragmentation, leak |
| Test order matters | Shared global state |

### Step 3 — Tool selection

| Bug class | Tool |
|---|---|
| Data race (Go) | `go test -race`, `go build -race` |
| Data race (Rust) | `cargo test`, ThreadSanitizer via flag |
| Data race (C/C++) | TSan: `clang -fsanitize=thread` |
| Memory error | ASan: `-fsanitize=address`; Valgrind |
| Uninitialized memory | MSan: `-fsanitize=memory`; Valgrind |
| Concurrency (any) | Stress-test with many threads, randomize scheduling |
| TOCTOU | Linux: `inotify`/`fanotify` to observe filesystem |
| GC / heap | Profiling tools per language |

These tools are **slow** (often 2-10x) but they catch what you can't see.

## Reproducing Heisenbugs

### Stress

Run the suspected operation thousands of times:

```bash
for i in $(seq 1 10000); do
    ./repro.sh || { echo "failed at $i"; break; }
done
```

If 1-in-10000, you'll see it. If 1-in-billion, won't.

### Parallelize

```bash
for i in 1 2 3 4; do ./repro.sh & done; wait
```

Race conditions emerge with concurrent access.

### Slow down / speed up

Add delays in specific places:

```python
def critical_section():
    time.sleep(0.001)  # let other threads catch up
    ...
```

Or remove existing throttling. Disrupt the timing that hides the bug.

### Randomize schedules

Some test frameworks support randomized test ordering / threading.
Use them.

For Go:
```bash
GOMAXPROCS=2 go test -count=100 ./...
```

For Rust async:
```bash
LOKI_RANDOMIZE_SCHEDULER=1 cargo test
```

### Memory poisoning

In languages with debug allocators (Go's `-race`, Rust's miri,
Valgrind): use them. They poison freed memory so use-after-free
becomes detectable.

## Fixing Heisenbugs

The fix depends on the class.

### Race conditions

Use the language's synchronization primitives:

- Locks (mutex, rwlock).
- Channels (Go), actors (Erlang), CSP-style.
- Lock-free structures with atomic operations (only if you really know
  what you're doing).
- Immutable data (functional core).

The single most reliable rule: **never share mutable state without a
lock or channel.**

### Uninitialized data

- Initialize at declaration, not "later."
- Use languages/types that prevent it (most modern type systems do).
- For FFI: zero-fill explicitly.

### TOCTOU

Use atomic operations:
- `os.O_CREAT | O_EXCL` — atomic file creation.
- Database row-level locks, `SELECT ... FOR UPDATE`.
- Compare-and-swap (CAS) for in-memory.

The pattern: **check and act in one operation, not two.**

### Optimizer / UB

- Use volatile / atomic where required.
- Don't rely on undefined behavior.
- Read the language's spec carefully.

This is mostly a C/C++ problem, less in modern safer languages.

## After You Fix

Write a test that **would have caught it**:

```rust
#[test]
fn no_race_in_concurrent_writes() {
    let counter = Arc::new(Mutex::new(0));
    let handles: Vec<_> = (0..100)
        .map(|_| {
            let c = counter.clone();
            std::thread::spawn(move || {
                let mut g = c.lock().unwrap();
                *g += 1;
            })
        })
        .collect();
    for h in handles { h.join().unwrap(); }
    assert_eq!(*counter.lock().unwrap(), 100);
}
```

Run with `--release` and with sanitizers if applicable. Heisenbug fixes
have a habit of regressing.

## When Logging "Fixes" the Bug

**Logging never fixes a Heisenbug.** It hides it.

If adding a log line makes the bug go away, the bug is still there. The
timing changed; under the right conditions (different machine, different
load), it'll come back.

Don't ship "fixes" that are just `log.debug(...)` calls. Either find the
actual cause or document the workaround explicitly as such.

## When Heisenbugs Are Too Hard

For 99% of devs, complex concurrency bugs are out of reach to fix
solo. Don't be ashamed to:

- Ask for help (the concurrent-programming expert in the team).
- File a detailed bug with reproducer and analysis, even if you can't
  fix it.
- Add observability so others have data when investigating.

## Production Heisenbugs

The hardest variant: a bug only seen in production, with no repro.

Strategy:
- Add detailed logging / tracing for the suspected area.
- Wait.
- Capture context when it happens.
- Iterate.

This takes weeks but is sometimes the only way.

## See Also

- [when-you-cant-repro.md](when-you-cant-repro.md) — related but distinct
- [../14-advanced/working-in-distributed-systems.md](../14-advanced/working-in-distributed-systems.md) — distributed heisenbugs
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md) — sanitizers, profilers, etc.
