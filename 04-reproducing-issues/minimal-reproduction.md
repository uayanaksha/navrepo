# Minimal Reproduction (MRE)

A Minimal Reproducible Example is a script or set of steps that
**triggers the bug every time** in the **smallest possible context**.

It's the most valuable artifact in a bug investigation. Once you have
it, the fix is usually straightforward. Without it, you're guessing.

## What "Minimal" Means

Minimal isn't "small for its own sake." It's "every part of this is
necessary to reproduce the bug."

To minimize:

- Remove unrelated config, plugins, middleware.
- Inline calls where you can — fewer layers, fewer suspects.
- Reduce data to the smallest input that still fails.
- Strip the UI if the bug is in the server.
- Strip the server if the bug is in the client.

If you remove something and the bug *still* reproduces — keep it removed.
If removing breaks the repro — put it back.

## What "Reproducible" Means

- Runs deterministically (every time, not sometimes).
- Independent of your machine (works on a fresh checkout).
- Fast (ideally < 1 minute).
- No manual steps (a script, not a tutorial).

A "reproducible 1 in 5 times" bug isn't reproducible enough. Keep
reducing until it's 100%. (Or, if truly probabilistic — see
[heisenbugs.md](heisenbugs.md).)

## The Reduction Process

### Step 1 — Start from the report

Whatever the user/colleague wrote, take it as the starting set of
conditions:

> "When I POST to /api/orders with two items, sometimes the second
> item's quantity is set to 0."

That's a hypothesis to test. Try reproducing it as-is.

### Step 2 — Reproduce in your environment

Get *any* reproduction. Don't worry about minimality yet. Just confirm
you can see the bug.

If you can't reproduce at all, jump to [when-you-cant-repro.md](when-you-cant-repro.md).

### Step 3 — Strip the irrelevant

One change at a time:

- Try with one item instead of two.
- Try with a simpler payload (remove optional fields).
- Try without authentication if not required.
- Try in a smaller environment (no caching layer, no queue).

After each strip, **re-test**. If still reproduces, keep the strip. If
not, revert.

### Step 4 — Isolate the layer

Once you have a minimal *external* repro, try to push it deeper:

- Can you reproduce by calling the service function directly (skipping HTTP)?
- Can you reproduce by calling the repo function directly (skipping service)?
- Can you write a unit test that fails?

Each layer you drop makes debugging faster.

### Step 5 — Lock it in

Save the repro:

```bash
# repro.sh
#!/usr/bin/env bash
set -euo pipefail

# fresh DB
psql ... < testdata/seed.sql

# trigger the bug
curl -fsS -X POST http://localhost:8080/api/orders \
    -H 'Content-Type: application/json' \
    -d @testdata/two-items-payload.json

# check the result
result=$(psql ... -c "SELECT quantity FROM order_items WHERE order_id='X' AND item_id='Y'")
if [[ "$result" == "0" ]]; then
    echo "BUG: quantity is 0"
    exit 1
fi
```

Now you can:
- Run `repro.sh` between code edits to verify your fix.
- Pass `repro.sh` to `git bisect run`.
- Share `repro.sh` with maintainers.

## MRE in Different Forms

### Failing unit test

The ideal MRE for library code:

```python
def test_two_items_second_quantity_not_zeroed():
    order = make_order(items=[
        Item(id="a", quantity=2),
        Item(id="b", quantity=3),
    ])
    order.commit()
    assert order.items[1].quantity == 3
```

Add it to the test suite (or a separate "regressions" file). Once it
passes, you've fixed the bug.

### Failing integration test

For service code:

```python
def test_post_two_items_preserves_second_quantity():
    response = client.post("/api/orders", json={
        "items": [{"id": "a", "qty": 2}, {"id": "b", "qty": 3}]
    })
    order_id = response.json()["id"]
    saved = db.get_order(order_id)
    assert saved.items[1].quantity == 3
```

### Shell script

For tools, CLIs, or services without a clean test harness:

```bash
#!/usr/bin/env bash
echo "before fix" | ./my-tool --process > out.txt
grep -q "expected output" out.txt || { echo "FAIL"; exit 1; }
```

### Containerized repro

For environment-sensitive bugs:

```dockerfile
FROM python:3.10
RUN pip install <project>==1.2.3
COPY repro.py .
CMD ["python", "repro.py"]
```

Build, run, observe. Self-contained.

## What Not to Include in an MRE

When sharing the MRE (issue, PR, ticket):

- **No company-specific data.** Use sample/anonymized data.
- **No internal hostnames/URLs.** Use localhost or stubs.
- **No "real" credentials.** Use obvious placeholders.
- **No unnecessary dependencies.** Pull in only what's needed.

## The "Five-Line MRE" Aspiration

The gold standard: an MRE that fits in five lines of code.

```python
import library
result = library.process({"items": [1, 2]})
assert result[1] == 2  # fails — gets 0
```

You can't always achieve this, but trying to often surfaces the root
cause: if you can't strip below 50 lines, the bug is likely about
interactions, not a single function.

## MRE as Communication

When filing a bug:

> "When I POST to the orders endpoint with two items, sometimes the
> quantity field gets zeroed out."

vs.

> "**Repro** (5 lines):
> ```python
> client.post("/api/orders", json={...})
> ```
> Expected: items[1].quantity == 3. Actual: 0.
> Reproduces 100% on commit abc123, OS X, Python 3.11."

The second gets fixed within days. The first might never get fixed.

## When MRE Is Hard

Some bugs resist easy MRE:

- **Time-of-day dependent** — only happens at midnight UTC.
- **Load-dependent** — only at 1000 RPS.
- **Customer-data dependent** — only with specific input shapes you
  haven't found.
- **Heisenbugs** — change behavior when observed.

For these, see:
- [when-you-cant-repro.md](when-you-cant-repro.md)
- [heisenbugs.md](heisenbugs.md)

## See Also

- [environment-parity.md](environment-parity.md) — why your env might not match the report
- [bisecting.md](bisecting.md) — once you have a repro, find the breaking commit
- [../05-fixing-issues/test-first-fixing.md](../05-fixing-issues/test-first-fixing.md) — the MRE-as-test workflow
