# Build a Toy

When a concept won't stick after reading and example-watching, **build
the smallest version of it from scratch.** The shortcut to intuition
is implementing the thing badly once.

## Why It Works

Reading creates passive knowledge.
Writing creates active knowledge.

Building a toy forces you to:
- Make every decision.
- Hit every edge case.
- Confront what you don't know.
- Internalize the structure.

After 100 lines of bad implementation, you'll understand a concept
better than after 100 pages of reading.

## What to Build

### Concept toys

Want to understand:

| Concept | Toy |
|---|---|
| Event loops | A simple `async/await` runtime in your language |
| Promises / futures | A toy `Promise` class |
| Generators / coroutines | Implement `range()` from scratch |
| Type inference | A toy Hindley-Milner inferencer |
| Garbage collection | A mark-and-sweep collector for a tiny VM |
| Hash maps | Open-addressing hash map |
| Trees / BSTs | Insert, find, delete |
| Database engine | LSM-tree or B-tree from scratch |
| Compilers | Parse and interpret arithmetic expressions |
| Web server | Listen on TCP, parse HTTP, respond |

These are classics for a reason — they bottle a concept into a finite
exercise.

### Library toys

For an unfamiliar library:

- Build a minimal usage example. 30 lines.
- See it work.
- Modify to break it, observe the failure.
- Modify to handle that failure.
- Now you understand the library's basic usage.

### Pattern toys

For a design pattern:

- Implement it in a tiny domain (e.g., a sandwich shop, not a real
  business).
- Note what's painful or unclear.
- Refactor once.
- Note what improved.

Patterns become intuition faster from doing than reading.

## How to Build

### 1. Scope minimally

The first version should be:

- < 100 lines.
- Single file.
- No tests (yet).
- No error handling.
- No optimization.

Aim for "compiles and runs the happy path."

### 2. Iterate fast

- Run after each change.
- Print intermediate values.
- Get to "I can see it working" within 30 minutes.

### 3. Expand carefully

Once it works:

- Add one edge case.
- Add one variant.
- Add tests if useful.

Don't try to build the full thing on the first pass.

### 4. Throw it away

Toys are throwaway. Their purpose is in the building.

Don't:
- Polish them.
- Try to ship them.
- Maintain them.

Save the file if you want a reference, but treat it as scratch.

## Examples in Practice

### Example: understanding async

A 50-line "async runtime" in Python:

```python
import time, heapq

class Task:
    def __init__(self, coro):
        self.coro = coro
        self.next_run = 0

def schedule(coro):
    return Task(coro)

def run(*coros):
    queue = [schedule(c) for c in coros]
    heapq.heapify(queue)
    while queue:
        task = heapq.heappop(queue)
        sleep_for = task.next_run - time.time()
        if sleep_for > 0:
            time.sleep(sleep_for)
        try:
            delay = task.coro.send(None)
            task.next_run = time.time() + (delay or 0)
            heapq.heappush(queue, task)
        except StopIteration:
            pass

async def example():
    print("a")
    await asyncio.sleep(0.1)
    print("b")
```

Running this, you'd discover:

- Async is just generators with a runtime.
- `sleep` yields back to the runtime.
- The runtime schedules tasks.

After this exercise, real `asyncio` makes much more sense.

### Example: understanding a parser

A tiny calculator parser:

```python
def parse(s):
    tokens = s.replace(' ', '')
    pos = [0]
    def parse_expr():
        left = parse_term()
        while pos[0] < len(tokens) and tokens[pos[0]] in '+-':
            op = tokens[pos[0]]; pos[0] += 1
            right = parse_term()
            left = (op, left, right)
        return left
    def parse_term():
        ... # similar for * and /
    return parse_expr()
```

Now ASTs and recursive descent make sense. You've internalized parsing.

### Example: understanding a hash map

A linear-probing hash map in your language:

- Array of (key, value) slots.
- Hash a key.
- If slot is taken, try the next.
- Handle deletion (tombstones).

After implementing this, "hash map" is no longer a black box.

## When to Reach for a Toy

- After reading docs / blog posts and still being confused.
- When the concept will recur in your work.
- When debugging requires understanding internals.
- When you're paid to know it well.

Not every concept needs a toy. But a few well-chosen toys per year
build deep intuition.

## Common Mistakes

### Building too much

You wanted to understand async. You built a full HTTP server. Now
you're learning HTTP, not async.

Keep toys narrow.

### Trying to ship the toy

Toys are not production code. Shipping them dilutes their purpose.

### Studying without building

"I'll just read a bit more first." You'll never feel ready. Start
building badly.

### Polishing the toy

Resist the urge to make it "nice." It's a thought experiment.

### Skipping the throw-away

Saving the toy as if it were valuable code. It's not — it's a learning
artifact.

## Toys for Learning Languages

When learning a new language, a few classic toys per language:

- A linked list.
- A simple HTTP client.
- A small CLI tool.
- A test runner.

Each forces you through different parts of the language.

## Multiple Toys for the Same Concept

Sometimes one toy isn't enough. Build:

- Toy A: a naive version.
- Toy B: a more sophisticated version.
- Compare.

The contrast is educational. Why did B need to be different? What
trade-offs did A have?

## Public Toys

Some people publish their toys (GitHub repos with "toy-X"). Useful:

- For your future reference.
- For others learning the same thing.
- As portfolio evidence.

Just be clear: "this is a toy, not for production."

## See Also

- [just-enough-learning.md](just-enough-learning.md)
- [lsp-as-tutor.md](lsp-as-tutor.md)
- [../14-advanced/reading-academic-papers.md](../14-advanced/reading-academic-papers.md) — sometimes papers + toy = real understanding
