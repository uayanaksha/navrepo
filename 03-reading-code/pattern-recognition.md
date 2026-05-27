# Pattern Recognition

The faster you read code, the more you're recognizing patterns. After
enough exposure, you stop reading line-by-line and start matching
shapes.

## Why Patterns Matter

A new codebase contains:

- **20% unique logic** — what makes this project specific.
- **80% pattern instances** — the same shapes that appear everywhere
  in software.

If you recognize the 80%, you can read it at glance-speed and focus on
the 20%.

## Common Architectural Patterns

### Layered architecture

```
Presentation → Application → Domain → Infrastructure
```

Look for folders like `handlers/`, `controllers/`, `services/`, `repos/`,
`storage/`. Each layer should not skip the next. Skipping is the bug.

### Hexagonal / ports-and-adapters

The domain doesn't know about HTTP or DB. Adapters bridge it.

```
└── domain/         (pure logic, no I/O)
└── ports/          (interfaces the domain defines)
└── adapters/
    ├── postgres/   (implements the storage port)
    ├── http/       (drives the domain via REST)
    └── kafka/      (drives the domain via events)
```

If you see this, the rule is: **the domain stays clean.**

### Event-driven

Events are first-class. Look for event types, dispatchers, subscribers.

```
└── events/
└── handlers/       (per-event)
└── projections/    (read models built from events)
```

Tracing a flow means following an event, not a call chain.

### CQRS

Read and write paths separated:

```
└── commands/       (write side)
└── queries/        (read side)
```

The two paths may use different stores (write to Postgres, read from
a denormalized view).

### Pipeline / DAG

Data transformations chained:

```
input → parse → enrich → validate → transform → write
```

Each step is a function/class. Errors propagate or fail-fast.

### Plugin / strategy

A core defines an interface; plugins implement it.

```
└── core/
└── plugins/
    ├── plugin_a/
    ├── plugin_b/
    └── plugin_c/
```

Adding behavior = adding a plugin. The core doesn't change.

## Common Code Idioms

### The "do everything" function

```python
def process(*args, **kwargs):
    # 200 lines
```

Often a sign of accumulated changes. Read top section to identify the
phases; treat each phase as a sub-function in your head.

### The repository pattern

```go
type UserRepo interface {
    Get(ctx context.Context, id string) (*User, error)
    Save(ctx context.Context, u *User) error
    List(ctx context.Context, filter Filter) ([]*User, error)
}
```

You'll see this everywhere. Once you recognize it, you know exactly
what to expect in each method.

### The handler / controller

```javascript
async function handler(req, res) {
    const input = parseInput(req);
    const result = await service.do(input);
    res.json(result);
}
```

Three phases: parse → call → respond. Some handlers add validation or
auth; the shape is constant.

### The middleware

```typescript
function middleware(next) {
    return async (req, res) => {
        // before
        const result = await next(req, res);
        // after
        return result;
    };
}
```

Wrapping pattern. Once you see it, you know where logging, auth,
metrics, etc. live.

### The factory

```python
def create_widget(config):
    if config.kind == 'a':
        return WidgetA(config)
    elif config.kind == 'b':
        return WidgetB(config)
```

Centralizes construction logic. Look for these when wondering "how is X
created?"

### The visitor

```java
interface Visitor<R> {
    R visitFoo(Foo f);
    R visitBar(Bar b);
}
```

Used for tree-walking (compilers, ASTs). Each node accepts a visitor;
the visitor implements per-type behavior.

### The observer / publisher-subscriber

```rust
emitter.on("event", handler);
emitter.emit("event", data);
```

Used for decoupling. Look for the event registration site to know who
listens.

### The state machine

```python
class Order:
    def submit(self):
        assert self.state == Created
        self.state = Submitted
    def fulfill(self):
        assert self.state == Submitted
        self.state = Fulfilled
```

Method behavior depends on state. Look for explicit state fields and
assertions; sometimes the state machine is implicit in method names.

### The pipeline / iterator chain

```rust
items.iter()
     .filter(|x| x.is_active)
     .map(|x| transform(x))
     .collect()
```

Read each stage independently. Errors usually short-circuit.

## Common Error-Handling Patterns

### Result types (Rust, Haskell-ish)

```rust
fn parse(s: &str) -> Result<Config, ParseError>
```

Caller must handle. `?` propagates.

### Exceptions (Python, Java, JS)

```python
try:
    ...
except SpecificError as e:
    ...
```

Caller may or may not handle; depends on convention.

### Multi-return (Go)

```go
result, err := doThing()
if err != nil {
    return nil, err
}
```

The classic Go shape. Once recognized, it reads as background noise.

### Panic / unwrap (Rust, Go, Python)

```rust
let result = thing().unwrap();  // panic on error
```

Indicates "should never fail" or "we don't handle this." Often a sign
of either confidence or laziness — context-dependent.

## Common Resource-Management Patterns

### Context manager (Python)

```python
with open(file) as f:
    ...
```

Resource cleaned up on exit.

### `defer` (Go)

```go
file, _ := os.Open(...)
defer file.Close()
```

Runs at function exit.

### RAII (C++, Rust)

Resource freed when the object goes out of scope.

### try-with-resources (Java)

```java
try (var conn = ds.getConnection()) {
    ...
}
```

All accomplish the same thing: deterministic cleanup. Recognize the
shape; trust the cleanup happens.

## Patterns That Signal Problems

### The god class

A class with 50+ methods and a vague name like `Manager`, `Handler`,
`Util`. Often grew over years; usually the first refactor candidate.
But see [chestertons-fence.md](chestertons-fence.md) — sometimes the
god class is load-bearing in a way that's hard to factor.

### The lasagna code

Deep inheritance chains, where finding the actual implementation
requires walking up many levels.

### The spaghetti

Shared mutable state, callbacks from anywhere, no clear ownership.
Common in older JS/PHP/C++.

### Premature abstraction

Three levels of indirection wrapping a single line of work. Often
introduced by enthusiastic developers who anticipated needs that never
materialized.

### Dead code

Functions never called, but kept around. Often Chesterton's fence —
investigate before deleting.

### Commented-out code

A real anti-pattern. Either the code is needed (uncomment it) or it's
not (delete it). "Just in case" is what `git log` is for.

## Building Pattern Vocabulary

Reading many codebases is the only way to absorb patterns. Some
approaches:

- **Read one new project a month.** Doesn't matter what — variety is the
  goal.
- **Follow blogs and conference talks** that dissect real architectures.
- **Read postmortems** — they often reveal what *should have been* a
  pattern but wasn't.
- **Study established design pattern catalogs** (GoF, Hohpe's EIP),
  but with skepticism — many patterns have aged poorly.

## Pattern-Reading vs Pattern-Following

Recognizing patterns is reading-time. *Imposing* patterns is design-time.

The danger: pattern recognition gets a grip and you start seeing the
pattern even where it doesn't apply. Resist. Patterns are tools, not
goals.

## See Also

- [reading-strategies.md](reading-strategies.md)
- [../09-unknown-tech/cargo-cult.md](../09-unknown-tech/cargo-cult.md) — patterns you shouldn't follow blindly
- [../15-language-ecosystems/](../15-language-ecosystems/) — per-language idioms
