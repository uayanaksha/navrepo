# Debugging Toolkit Deep Dive

[../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)
covered *what* the tools are and when to reach for each. This goes
deeper on the heavy artillery: the techniques that crack the bugs print
statements and a basic debugger can't.

## The Mindset First

Deep debugging is the scientific method under pressure:

1. **Observe** the failure precisely. What exactly happens, when?
2. **Hypothesize** a cause.
3. **Predict** what you'd see if the hypothesis were true.
4. **Test** with the smallest possible experiment.
5. **Repeat**, narrowing each time.

The tools below are instruments for *observing* and *testing* — they
don't replace the reasoning. The most common deep-debugging failure is
flailing with powerful tools instead of forming hypotheses.

## Profilers and Flame Graphs

Beyond "which function is hot," profilers answer *how the time is spent*
when you know how to read them.

### Flame graphs

A flame graph turns a profile into a picture: the x-axis is *proportion
of samples* (width = time spent), the y-axis is *stack depth* (call
nesting). You read it like this:

- **Wide frames = expensive.** A wide box means lots of time was spent in
  that function (and its callees). Width is the only thing that matters;
  left-to-right order is just alphabetical, not chronological.
- **Plateaus at the top** = where the CPU actually is (leaf functions
  doing the work).
- **Towers** = deep call chains.

Scan for the widest boxes near the top — that's your hot path. The
discipline of *acting* on this is in
[performance-investigation.md](performance-investigation.md).

### On-CPU vs off-CPU

- **On-CPU profiling** (the usual kind) shows where the CPU burns
  cycles. Great for compute-bound code.
- **Off-CPU profiling** shows where threads are *blocked* (waiting on
  I/O, locks, sleeps). Essential when the program is *slow* but the CPU
  is *idle* — the time is going to waiting, which on-CPU profiling can't
  see. If CPU usage is low but it's still slow, you need off-CPU
  analysis.

### Allocation profiling

CPU isn't the only cost. Allocation profilers show *where memory is
allocated*, surfacing the churn that drives GC pressure and cache
misses. Often the real fix for a "slow" hot loop is "stop allocating in
it," visible only in an allocation profile.

## eBPF: Observe Anything, Live

On modern Linux, **eBPF** lets you run small, safe programs in the
kernel to observe almost anything — syscalls, network, disk, function
entry/exit — with very low overhead, on a *running production system*,
without recompiling or restarting.

The `bcc` and `bpftrace` toolkits expose this:

```bash
# Who's opening files, live?
opensnoop-bpfcc

# Histogram of a syscall's latency
funclatency-bpfcc -d 10 vfs_read

# Ad-hoc: count syscalls by process (bpftrace one-liner)
bpftrace -e 'tracepoint:raw_syscalls:sys_enter { @[comm] = count(); }'
```

The "BPF performance tools" ecosystem (Brendan Gregg's work) provides
ready-made tools for disk, network, scheduler, and memory analysis.
eBPF is the answer to "I need to see what production is doing *right
now* without touching the app." It's the modern, lower-overhead
successor to a lot of what `strace`/`dtrace` did.

## rr: Time-Travel Debugging

The deep version of [../04-reproducing-issues/heisenbugs.md](../04-reproducing-issues/heisenbugs.md)'s
hardest cases. `rr` records execution once, then replays it
deterministically — including running **backwards**.

The killer workflow:

```bash
rr record ./buggy-program
rr replay
# In the debugger:
(rr) continue            # run to the crash / bad state
(rr) watch -l corrupted_var
(rr) reverse-continue    # run BACKWARDS to the moment it was last written
```

You start at the *symptom* and walk backward to the *cause* — the exact
instruction that set the wrong value. This inverts normal debugging
(where you guess where to put a breakpoint *before* the bug) and is
transformational for "how did this variable ever become this?" Mostly
Linux/x86.

## Core Dumps: Autopsy After Death

When a process crashes (especially in production, where you can't attach
live), a core dump is a frozen snapshot of its memory at the moment of
death.

```bash
ulimit -c unlimited                 # enable dumps
# ... crash happens, core written ...
gdb ./program core                  # open the corpse
(gdb) bt full                       # full backtrace with locals
(gdb) info threads                  # all threads' states
(gdb) thread apply all bt           # every thread's stack
```

On systemd systems, `coredumpctl list` / `coredumpctl gdb <name>`
manages them. Core dumps are how you debug a crash you can't reproduce
on demand — the crash *is* the artifact. For managed runtimes, the
equivalent is a heap dump / thread dump (e.g., JVM `jmap`/`jstack`, Go
panic stacks, .NET dumps).

## Packet Capture: Wireshark

When the bug is on the wire (see
[../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md) for
`tcpdump` basics), Wireshark is the deep analysis tool:

- **Follow a TCP/HTTP stream** to see a whole conversation reassembled.
- **Filter** precisely (`http.response.code >= 500`, `tcp.analysis.retransmission`).
- **Diagnose TLS handshake failures**, retransmissions, resets, and
  protocol-level confusion you can't see from application logs.
- For HTTP(S) specifically, an intercepting proxy (`mitmproxy`) is often
  friendlier and can decrypt TLS you control.

## Choosing the Heavy Tool

```
Slow, CPU pegged          → on-CPU profiler + flame graph
Slow, CPU idle            → off-CPU profiler (it's blocked/waiting)
Memory growth / GC churn  → allocation / heap profiler
"What is prod doing NOW?" → eBPF (bpftrace / bcc tools)
"How did this state arise?" → rr, reverse-continue
Crash you can't reproduce → core dump + gdb
Bug between machines      → Wireshark / tcpdump / mitmproxy
```

## Anti-Patterns

### Powerful tools, no hypothesis

Running `perf`, eBPF, and `rr` while flailing, with no theory to test.
The tools observe; *you* reason. Form a hypothesis first.

### On-CPU profiling an I/O-bound program

Wondering why the profile is empty when the program is slow but idle —
the time is in *waiting*, which needs off-CPU analysis.

### Ignoring allocation cost

Optimizing CPU in a hot loop that's actually bottlenecked on allocation
and GC. Check the allocation profile.

### Not capturing the core

A rare production crash happens, no core dump was enabled, and it's
gone. Enable dumps *before* you need them so the next crash is
debuggable.

### Learning the tool during the fire

Reading the `bpftrace` or `rr` manual mid-incident. Spend an hour on
each *before* you need it (see [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)).

## See Also

- [performance-investigation.md](performance-investigation.md)
- [../11-tooling/debugging-tools.md](../11-tooling/debugging-tools.md)
- [../04-reproducing-issues/heisenbugs.md](../04-reproducing-issues/heisenbugs.md)
- [../04-reproducing-issues/observability.md](../04-reproducing-issues/observability.md)
- [incident-response.md](incident-response.md)
