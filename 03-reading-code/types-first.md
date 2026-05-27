# Types First

The shape of data tells you a surprising amount about a system. **Read
types before bodies. Read signatures before implementations.**

## The Principle

When you encounter a function:

```go
func ProcessOrder(ctx context.Context, o *Order) (*Receipt, error)
```

…you already know:
- It's cancellable / timed (context).
- It needs an Order.
- It produces a Receipt or an error.
- It does *not* mutate anything else externally visible (return values only).

That's most of what you need before reading the body.

## What Types Tell You

### The shape of the domain

Read all type/struct/class definitions in a module before reading
functions. You'll absorb:

- The vocabulary (Order, Receipt, Customer, Inventory).
- The relationships (Order has many Items, references a Customer).
- The constraints (which fields are optional, which are validated).
- The persistence boundary (what's stored vs computed).

### The error model

```rust
fn parse(s: &str) -> Result<Config, ParseError>
```

Versus:

```python
def parse(s: str) -> Config:  # raises ParseError
```

Same function, different ergonomics. Reading the signature tells you
how errors propagate.

### Mutability

```rust
fn process(&mut self, order: Order) -> Receipt
```

`&mut self` says this function changes object state. If you see `&self`
(immutable), the function is presumably pure-ish.

In Go: pointer receivers (`*T`) usually signal mutation. Value receivers
(`T`) usually signal pure functions.

In Python/JS, the type system doesn't say — you have to read the body
or convention.

### Concurrency

Types often expose concurrency primitives:

- `Mutex<T>`, `RwLock<T>` — protected data.
- `Arc<T>` — shared ownership.
- `chan T` — channels (Go).
- `Future<T>`, `Promise<T>`, `async fn` — async boundaries.

Spot these in signatures to know what concurrency model the system uses.

## Reading Order in a New File

1. **Imports** — what does this file depend on? Reveals layer.
2. **Top-level types** — the data model.
3. **Constants / errors** — the configured surface.
4. **Public function signatures** — the API.
5. **Function bodies** — only if needed.

This top-to-bottom-but-skipping-bodies pass takes 1–2 minutes per file.

## Type-Light Languages

In Python (untyped), JS (untyped), Ruby, older Lua:

- Read **docstrings** as types.
- Read **the first few lines** of the function (often shows expected
  shape).
- Read **tests** for examples.
- Use **type inference tools** (mypy, pyright, TS for JS via JSDoc) if
  the project hasn't.

If the project doesn't have types and you're contributing meaningfully,
adding types incrementally is often welcome. See
[../13-hidden-knowledge/docs-as-contribution.md](../13-hidden-knowledge/docs-as-contribution.md).

## Reading Interfaces / Traits / Protocols First

When a project uses interfaces heavily, read the interface before the
implementations.

```go
type Storage interface {
    Get(ctx context.Context, id string) (*User, error)
    Save(ctx context.Context, u *User) error
}
```

This tells you:
- What every Storage implementation must do.
- The contract for swapping implementations.
- Where mocks live.

Then implementations (`PostgresStorage`, `InMemoryStorage`, `MockStorage`)
slot into a known frame.

## Reading Generic / Type Parameters

```rust
fn process<T: Serialize + Send + 'static>(item: T) -> Result<Bytes, Error>
```

The bounds tell you:
- `T` must be `Serialize` — there's serialization happening.
- `T` must be `Send` — moved across threads.
- `T: 'static` — no borrowed references.

In Java/C#:

```java
<T extends Comparable<T> & Closeable> void process(T item)
```

…tells you it sorts and closes.

Read bounds first; they constrain what the function can do.

## Inferred Types and Inlay Hints

For languages with inference (Rust, Go's `:=`, TypeScript, Kotlin), the
declared variable doesn't show its type. Modern editors show **inlay
hints**:

```rust
let result/*: Result<Vec<User>, Error>*/ = fetch_users();
```

Turn on inlay hints. They're free reading aid.

## Reading Generated / Protobuf Types

For RPC services, the `.proto` file (or equivalent IDL) is the canonical
type definition:

```proto
message User {
  string id = 1;
  string email = 2;
  google.protobuf.Timestamp created_at = 3;
}
```

Read these first when working in RPC-heavy systems. They're more reliable
than generated code, and they're the cross-language ground truth.

## When Types Lie

Types are usually accurate, but watch for:

- **`interface{}`, `any`, `Object`** — types that mean nothing.
- **`json.RawMessage`, `bytes`** — opaque data; need separate context.
- **String-typed "rich" data** — `userId string` could be email, UUID,
  or numeric. Check.
- **Deprecated types not removed** — sometimes a type exists for backward
  compatibility but isn't current.

Trust types, verify when stakes are high.

## Practical Exercise

In a file you don't know:

1. Read the file's imports.
2. List its type/class definitions in your scratchpad.
3. List its public function signatures.
4. Predict what it does, in one sentence.
5. Read one body to verify.

Repeat 10 times; you'll have built deep intuition for "shape-reads-as".

## See Also

- [reading-strategies.md](reading-strategies.md) — where types-first fits
- [tests-as-docs.md](tests-as-docs.md) — tests show types in use
- [../09-unknown-tech/lsp-as-tutor.md](../09-unknown-tech/lsp-as-tutor.md) — LSP-as-types-tutor
