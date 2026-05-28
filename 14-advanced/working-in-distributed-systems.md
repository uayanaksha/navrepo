# Working in Distributed Systems

In a distributed system, the parts run on different machines, fail
independently, and communicate over an unreliable network. Reasoning
that works for a single process breaks here. This is the survival
vocabulary.

## The Core Reality

The "fallacies of distributed computing" name the assumptions that will
burn you. The network is **not** reliable, **not** instant, **not** free,
and **not** secure; bandwidth is finite; topology changes; there's no
single administrator. Every one of these is a bug class.

The practical consequence: **anything that can fail, will — partially,
intermittently, and at the worst time.** A call to another service might
succeed, fail, time out, or *succeed but the response is lost*. Your
code has to be correct under all of those.

## Observability Across Boundaries

In one process you read a stack trace. Across services, a request hops
through many processes — you need to *stitch* its journey together.

### Correlation IDs

Assign each incoming request a unique ID and **propagate it** through
every downstream call, log line, and message. Then you can grep the
entire path of one request across every service.

```
[req-7f3a] gateway: received POST /order
[req-7f3a] order-svc: reserving inventory
[req-7f3a] inventory-svc: insufficient stock → 409
[req-7f3a] order-svc: returning 409 to client
```

Without a correlation ID, the logs of ten services are ten haystacks.
With it, one `grep req-7f3a` reconstructs the story. This is the
single most important distributed-debugging tool.

### Distributed tracing

The structured version: tools like OpenTelemetry-based tracers
(Jaeger, Tempo, Zipkin, and commercial APMs) record a **trace** of
**spans** — each span a unit of work, nested to show the call tree and
where the time went.

```
trace ├─ gateway            [120ms]
      └─ order-svc          [110ms]
           ├─ inventory-svc [ 40ms]
           └─ payment-svc   [ 60ms]  ← the latency lives here
```

Tracing answers "where did the time go?" and "what actually got called?"
across the whole system. Extends [../04-reproducing-issues/observability.md](../04-reproducing-issues/observability.md)
to the multi-service world.

## Idempotency

Because a request may be retried (the response was lost, a timeout
fired, a queue redelivered), the *same operation can arrive more than
once*. An operation is **idempotent** if doing it twice has the same
effect as doing it once.

- "Set balance to 100" — idempotent (safe to repeat).
- "Add 100 to balance" — **not** idempotent (repeats corrupt the
  balance).

Make operations idempotent, usually with an **idempotency key**: the
client sends a unique key per logical operation; the server records
processed keys and ignores duplicates.

```
POST /charge   Idempotency-Key: order-7f3a-charge
# Server: if key already processed, return the prior result; don't charge again.
```

This is the antidote to the retry problem below. Payment systems live
and die by it.

## Retries (and the Storms They Cause)

When a call fails, retrying often works — the failure was transient. But
naive retries are dangerous:

- **Retry only idempotent operations** (or use idempotency keys), or you
  double-charge, double-send, double-write.
- **Back off exponentially with jitter.** Immediate, synchronized
  retries from many clients create a **thundering herd / retry storm**
  that turns a blip into an outage. Randomized, increasing delays spread
  the load.
- **Cap retries and use a circuit breaker.** After N failures, *stop
  calling* the struggling service for a while — hammering a dying
  service prevents its recovery. The circuit "opens," fails fast, then
  tentatively "half-opens" to test recovery.
- **Set timeouts on everything.** A call with no timeout can hang
  forever, exhausting your connection pool and cascading the failure
  upstream.

## Consistency Models

Distributed data can't be both perfectly consistent and perfectly
available under network partitions (the CAP intuition). So systems pick:

| Model | Meaning | Implication |
|---|---|---|
| **Strong consistency** | Everyone sees the latest write immediately | Simpler to reason about; costs latency/availability |
| **Eventual consistency** | Replicas converge *eventually* | Reads may be stale; you must design for it |
| **Read-your-writes**, **monotonic reads**, etc. | Partial guarantees | The useful middle ground |

The trap: writing code that assumes a write is *immediately* visible
everywhere when the system is eventually consistent. You write a record,
immediately read it back from a replica, and get the *old* value. Know
your system's model and design reads accordingly (read from primary when
you need your own write; tolerate staleness when you don't).

## The Exactly-Once Myth

You will hear "exactly-once delivery." Over an unreliable network, **true
exactly-once *delivery* is impossible** — the sender can never be certain
a message arrived, so it must either risk losing it (at-most-once) or
risk duplicating it (at-least-once).

What's actually achievable is **exactly-once *processing*** (sometimes
"effectively-once"): use **at-least-once delivery** (retry until
acknowledged) **plus idempotency** (dedupe on the receiving side) so the
*effect* happens once even though the *message* may arrive many times.

> The honest framing: deliver at-least-once, process idempotently. Anyone
> promising exactly-once delivery with no idempotency is selling a myth.

## Ordering and Time

Two more single-process assumptions that break:

- **Messages can arrive out of order.** Don't assume the order you sent
  is the order received. Use sequence numbers or version vectors if order
  matters.
- **Clocks disagree.** Two machines' wall clocks differ (clock skew), so
  "later timestamp = later event" is unreliable across machines. For
  ordering, prefer logical clocks (Lamport/vector clocks) or a single
  source of truth, not wall-clock comparison.

## Failure Is a Design Input

Design *assuming* partial failure, because it's constant at scale:

- **Graceful degradation** — when a non-critical dependency is down,
  degrade (serve cached/partial results) rather than fail entirely.
- **Bulkheads** — isolate failures so one struggling component doesn't
  sink the whole system (separate pools/quotas).
- **Health checks and readiness** — let the system route around sick
  instances.
- **Backpressure** — when overloaded, shed or queue load deliberately
  rather than collapsing.

## Anti-Patterns

### Assuming the network is reliable

Code with no timeouts, no retries, no failure handling on remote calls.
The network *will* fail; design for it from the start.

### Retrying non-idempotent operations

Retrying "charge the card" without an idempotency key double-charges
customers. Make it idempotent before you make it retryable.

### Synchronized retries with no backoff

Every client retrying immediately turns a transient blip into a retry
storm that takes the service down. Exponential backoff with jitter,
always.

### Assuming immediate consistency

Reading your own write back from a replica and expecting the new value
on an eventually-consistent store. Know your consistency model.

### Trusting "exactly-once"

Building on a promise that's physically impossible. Deliver
at-least-once, dedupe idempotently.

### Ordering by wall-clock timestamp

Comparing timestamps from different machines to order events. Clocks
skew; use logical ordering.

## See Also

- [incident-response.md](incident-response.md)
- [performance-investigation.md](performance-investigation.md)
- [../04-reproducing-issues/observability.md](../04-reproducing-issues/observability.md)
- [../10-features-refactors/migration-strategies.md](../10-features-refactors/migration-strategies.md)
- [../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md)
