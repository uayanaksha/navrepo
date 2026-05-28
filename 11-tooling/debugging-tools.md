# Debugging Tools

`print()` gets you surprisingly far. But some bugs — races, memory
corruption, "it's slow," "it hangs," "it works in dev not prod" — need
tools that see what print can't. This is the map of what exists and
when to reach for it.

## The Layers

| Question | Tool class |
|---|---|
| What is this variable, right now? | Interactive debugger |
| What syscalls / files / network is it doing? | `strace`, `dtrace`, `lsof` |
| Where is the time going? | Profiler (`perf`, `pprof`, `py-spy`) |
| Where is the memory going / is it corrupt? | Sanitizers, Valgrind, heap profilers |
| It only fails sometimes — can I replay it? | `rr` (record/replay) |
| What's on the wire? | `tcpdump`, Wireshark |

Match the tool to the question. Reaching for a profiler to find a
logic bug, or a debugger to find a leak, wastes time.

## Interactive Debuggers

The baseline skill: set a breakpoint, step, inspect. Per language:

| Language | Debugger | Drop-in breakpoint |
|---|---|---|
| Python | `pdb` / `debugpy` | `breakpoint()` |
| Go | `dlv` (Delve) | `dlv debug ./cmd/...` |
| Rust / C / C++ | `lldb`, `gdb` | `rust-lldb target/debug/bin` |
| Node / TS | Chrome DevTools | `node --inspect-brk` |
| Java / Kotlin | JDB / IDE debugger | IDE breakpoint |

Most are far nicer through your editor's debug UI (see
[editor-and-lsp.md](editor-and-lsp.md)), but knowing the CLI matters
when you're on a remote box with no GUI.

Key commands are the same everywhere, just spelled differently:
`break`, `continue`, `step`, `next`, `print`, `backtrace`, `watch`.
Learn those six and you can drive any of them.

### Conditional and watchpoints

The features people forget exist:

```
# gdb / lldb: break only when a condition holds
break handler.c:42 if user_id == 1337

# watch: stop when a value changes (finds "who mutated this?")
watch global_counter
```

A conditional breakpoint beats stepping 10,000 times to reach the one
iteration that matters.

## Tracing Syscalls: strace / dtrace

When a program "does nothing," "can't find a file," or "hangs," trace
its syscalls. You see every file opened, every network call, every
block.

```bash
# Linux
strace -f -e trace=open,openat,connect ./myprogram      # filter syscalls
strace -f -p 12345                                       # attach to running PID
strace -f -T ./myprogram                                 # time each syscall

# macOS / BSD (strace equivalent)
sudo dtruss ./myprogram
```

Classic wins:
- "File not found" → see exactly which path it tried to open.
- "Hangs on startup" → see the `connect()` or `read()` it's blocked on.
- "Slow" → `-T` shows which syscall eats the time.

## What's It Touching: lsof

`lsof` lists open files, sockets, and ports.

```bash
lsof -p 12345                  # everything this process has open
lsof -i :8080                  # who's listening on / using port 8080
lsof -i -P -n | grep ESTAB     # established network connections
lsof +D /var/log               # who has files open under this dir
```

The fastest answer to "address already in use" (what's on the port)
and "can't unmount / delete" (who's holding the file).

## Profilers: Where the Time Goes

Never optimize by guessing. Profile, then fix the actual hot path.
(The discipline of this lives in
[../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md);
here are the tools.)

| Language | Profiler |
|---|---|
| Any (Linux, native) | `perf record` / `perf report`; flame graphs |
| Go | built-in `pprof` (`import _ "net/http/pprof"`) |
| Python | `py-spy` (sampling, no code change, attaches to running PID) |
| Rust | `cargo flamegraph`, `perf` |
| Node | `--prof`, `clinic`, Chrome DevTools profiler |
| JVM | `async-profiler`, JFR (Java Flight Recorder) |

```bash
# perf → flame graph (native code)
perf record -g ./myprogram
perf report                          # interactive
# or generate a flame graph with the FlameGraph scripts / `flamegraph`

# py-spy on an already-running process (no restart, no instrumentation)
py-spy top --pid 12345
py-spy record -o profile.svg --pid 12345

# Go: with net/http/pprof imported and serving
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30
```

`py-spy` deserves a special mention: it profiles a *running* Python
process without modifying it or restarting it. Perfect for "prod is
slow right now."

## Memory: Sanitizers and Valgrind

For native code (C, C++, sometimes Rust unsafe / FFI), memory bugs are
the hard ones. Sanitizers are compiler instrumentation that catches
them at runtime with a clear report.

| Sanitizer | Catches | Flag |
|---|---|---|
| **ASan** (Address) | Use-after-free, buffer overflow, leaks | `-fsanitize=address` |
| **TSan** (Thread) | Data races | `-fsanitize=thread` |
| **MSan** (Memory) | Use of uninitialized memory | `-fsanitize=memory` |
| **UBSan** (Undefined Behavior) | Signed overflow, bad shifts, null deref | `-fsanitize=undefined` |

```bash
# clang or gcc
cc -g -fsanitize=address,undefined -o prog prog.c && ./prog
```

ASan/UBSan are cheap enough to run in your test suite. TSan is the only
realistic way to find data races — you will not find them by reading.

**Valgrind** (`memcheck`) catches similar memory errors without
recompiling, at a heavy slowdown (10–50x). Prefer sanitizers when you
can rebuild; use Valgrind when you can't.

```bash
valgrind --leak-check=full ./prog
valgrind --tool=helgrind ./prog       # race detection
valgrind --tool=callgrind ./prog      # call-graph profiling (view with kcachegrind)
```

Managed languages (Go, Java, Python) have their own race detectors and
heap profilers — e.g. `go test -race`, JVM heap dumps + Eclipse MAT,
`tracemalloc` in Python.

## rr — Record and Replay (Linux)

The tool that turns heisenbugs into ordinary bugs. `rr` records a
program's execution once, then lets you replay it *deterministically*
as many times as you want — including stepping **backwards**.

```bash
rr record ./myprogram --flags
rr replay                 # opens in a gdb-like session
# inside: reverse-continue, reverse-step, reverse-next
```

Why it's transformational:
- An intermittent bug, once recorded, reproduces 100% on replay.
- `watch` a variable, then `reverse-continue` to the exact moment it
  got the wrong value — debugging *backwards* from the symptom to the
  cause.

Mostly Linux/x86. When it fits, nothing else compares for "it only
happens 1 in 50 runs." See
[../04-reproducing-issues/heisenbugs.md](../04-reproducing-issues/heisenbugs.md).

## On the Wire: tcpdump / Wireshark

When the bug is between machines:

```bash
sudo tcpdump -i any -w capture.pcap port 443     # capture to file
sudo tcpdump -i any -A 'port 8080'               # ASCII, live
```

Open the `.pcap` in Wireshark for a readable, filterable view of every
packet. Essential for "the request never arrives," TLS handshake
failures, and protocol-level confusion. For HTTP specifically, a proxy
like `mitmproxy` is often friendlier.

## Core Dumps: Debug After the Crash

When a process crashes, a core dump is a frozen snapshot you can open
in a debugger later.

```bash
ulimit -c unlimited            # enable core dumps in this shell
./prog                         # crashes, writes core
gdb ./prog core                # inspect the moment of death
# (bt) for the backtrace at crash time
```

`coredumpctl` (systemd) manages them on modern Linux:
`coredumpctl gdb <pid-or-name>`.

## Choosing the Right Tool, Fast

```
"It can't find/open something"     → strace / dtruss
"Address already in use"           → lsof -i :PORT
"It hangs"                         → strace -p (see the blocked syscall),
                                     or attach a debugger and `bt`
"It's slow"                        → profiler (perf / py-spy / pprof)
"It crashes / corrupts memory"     → ASan, then core dump + gdb
"It races"                         → TSan / go test -race / helgrind
"It only fails sometimes"          → rr record, then replay
"The network is weird"             → tcpdump / Wireshark / mitmproxy
```

## Anti-Patterns

### Profiling to find a logic bug

A profiler tells you *where time goes*, not *why the output is wrong*.
Use a debugger or tests for correctness; the profiler for speed.

### Optimizing without profiling

The hot path is almost never where you guess. Measure first, every
time. (More in
[../12-mindset/premature-optimization.md](../12-mindset/premature-optimization.md).)

### Print-debugging a race

Adding prints changes the timing and the race vanishes (a
heisenbug). Reach for TSan or `rr`, which observe without perturbing —
or perturb deterministically.

### Ignoring sanitizers because "it passes"

A C/C++ test suite that's green under no sanitizer can be riddled with
undefined behavior. Run the suite under ASan+UBSan at least in CI.

### Learning the tool during the incident

The middle of a production fire is the worst time to read the `perf`
man page. Spend 30 minutes on each of these *before* you need them.

## See Also

- [editor-and-lsp.md](editor-and-lsp.md)
- [../04-reproducing-issues/heisenbugs.md](../04-reproducing-issues/heisenbugs.md)
- [../04-reproducing-issues/observability.md](../04-reproducing-issues/observability.md)
- [../14-advanced/debugging-toolkit-deep-dive.md](../14-advanced/debugging-toolkit-deep-dive.md)
- [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)
