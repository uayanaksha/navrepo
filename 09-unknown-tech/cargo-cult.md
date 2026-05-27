# Cargo-Cult Carefully

Copying a pattern from elsewhere in the codebase is **good** — it
preserves consistency. But copy with eyes open.

## What Cargo-Culting Is

> "Cargo cult programming is a style of computer programming
> characterized by the ritual inclusion of code or program structures
> that serve no real purpose."

You see code that looks like:

```python
self._lock = threading.Lock()
```

…in many places. You add it to your new class without understanding
why. That's cargo-culting.

## When Copying Is Fine (Most of the Time)

For consistency, copying is good:

- Same logger setup as other files.
- Same error-wrapping pattern.
- Same test fixture style.
- Same way of laying out a handler.

Consistency reduces cognitive load for future readers. Match the
style.

## When Copying Is Dangerous

Copying becomes a problem when:

### The pattern is legacy

The other code is from 2018. The project has moved on. Recent code
uses a different style. Your copy adds noise.

Fix: read **recent** code, not old.

### The pattern is wrong but uncaught

The codebase has a subtle bug pattern. You spread it.

Fix: read tests too. If the pattern works *and* is tested, it's
probably right.

### The pattern fits a different context

The original pattern handles concurrent access. Yours doesn't need to.
Copying brings overhead without value.

Fix: understand the *why*. Only copy what applies.

### The pattern uses deprecated APIs

The codebase has old code using old APIs. Newer code uses new ones.
Your copy resurrects the old.

Fix: prefer code from recent commits.

## How to Copy Well

### 1. Find the canonical example

Not the first instance you find. The **best** one:

- Most recent.
- By a maintainer.
- In an area with high test coverage.

```bash
git log --oneline -n 20 --all -- '**/*similar-file*'
```

### 2. Understand the example

Before copying:
- What does each piece do?
- Why is each piece there?
- What's optional vs required?

If you can't explain it, you can't copy it well.

### 3. Adapt, don't transplant

Copy with intent:

- Rename variables to fit your context.
- Remove what doesn't apply.
- Adjust types / shapes.

A blind copy-paste produces tangled code.

### 4. Verify in context

After copying:
- Does this compile?
- Does this pass tests?
- Does this match the rest of *my* file?

## The "Why" Test

For each thing you copied, ask "why is this here?"

If the answer is "I don't know," investigate. If after investigation
the answer is still "I don't know," consider:

- Removing it.
- Leaving it but noting in a comment "copied from X; unsure if needed
  here."
- Asking a maintainer.

## Examples of Right and Wrong Copying

### Right

```python
# project's standard logger setup
log = logging.getLogger(__name__)
```

You add this to a new file. Same pattern, same purpose. Fine.

### Wrong (cargo cult)

```python
# you saw this in another file:
def __init__(self, *args, **kwargs):
    super().__init__(*args, **kwargs)
```

You copy it without thinking. Often unnecessary unless you're
overriding for a reason. Drop or understand why.

### Right with adaptation

```python
# original:
async def handle_payment(req: PaymentRequest) -> PaymentResponse:
    ctx = build_context(req)
    return await payment_service.process(ctx, req)

# your adaptation:
async def handle_refund(req: RefundRequest) -> RefundResponse:
    ctx = build_context(req)
    return await refund_service.process(ctx, req)
```

Same shape, adjusted types. Good.

### Wrong with adaptation

```python
# original (uses thread-local cache):
def get_user(user_id):
    if user_id in _thread_local.cache:
        return _thread_local.cache[user_id]
    user = db.fetch(user_id)
    _thread_local.cache[user_id] = user
    return user

# your "adaptation" (calls from a single-threaded context):
def get_widget(widget_id):
    if widget_id in _thread_local.cache:  # thread-local for single-threaded code??
        return _thread_local.cache[widget_id]
    ...
```

You copied thread-local cache without needing it. Now you've added
complexity without benefit.

## Patterns You Should NOT Copy Just Because They Exist

### `try: ... except Exception: pass`

Even if the codebase does this in three places. Each instance is
suspect.

### Sleep-based "synchronization"

```python
time.sleep(2)  # wait for something
```

Brittle. Don't propagate.

### Mutable global state

```python
_global_config = {}
```

Sometimes necessary. Often a smell. Don't add new ones without
thinking.

### "Defensive" wrappers

```python
def safe_get(d, k, default=None):
    try:
        return d.get(k, default)
    except:
        return default
```

`.get()` already handles this. Don't add unnecessary wrappers.

### Magic numbers without explanation

```python
TIMEOUT = 27  # ??
```

If you copy, also copy the comment explaining why.

## When the Codebase IS the Anti-Pattern

Sometimes the whole codebase has bad patterns. You can:

- **Match anyway.** Inconsistent code is worse than consistent-bad
  code, sometimes.
- **Match but flag.** Propose a refactor as a follow-up.
- **Improve carefully.** Only if you've earned trust.

For first PRs: match. For later PRs: improve.

## Pattern Sources Outside the Project

You might find patterns from:

- A previous job.
- Books / blogs.
- AI suggestions.

These don't automatically apply to *this* project. Check:

- Does this fit the project's style?
- Is this idiomatic for the language/framework as used here?
- Does the team prefer this?

When in doubt, match the project.

## See Also

- [code-search-as-teacher.md](code-search-as-teacher.md) — finding the patterns
- [../03-reading-code/pattern-recognition.md](../03-reading-code/pattern-recognition.md)
- [../03-reading-code/chestertons-fence.md](../03-reading-code/chestertons-fence.md)
