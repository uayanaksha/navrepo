# Migration Strategies

A migration moves the system from one state to another. The hard
migrations have two states coexisting for a while.

## Why This Is Hard

A naive migration:

1. Stop the system.
2. Convert everything.
3. Start with new state.

This doesn't work for:
- Live systems (downtime).
- Distributed systems (partial state).
- Multi-version clients (old clients still around).
- Long-running data (can't convert overnight).

So: **plan for the coexistence period.**

## The Expand-Contract Pattern

The most common migration shape. Four phases:

### 1. Expand

Introduce the new state alongside the old. Old and new both work.

### 2. Migrate

Move callers / data / users to the new state. Old still works for
laggards.

### 3. Contract (deprecate)

Mark old as deprecated. Strong signal to migrate.

### 4. Remove

After enough time, delete the old.

Each phase is its own deploy / release. Each is reversible (mostly).

## Database Schema Migrations

### Backwards-compatible (safe)

These can be deployed *before* code changes:

- Adding a column (nullable).
- Adding an index (non-blocking if your DB supports it).
- Adding a table.
- Adding a constraint **with no validation of existing rows** (when
  the DB supports this).

Deploy schema first; deploy code second. If code is rolled back,
schema is still valid.

### Code-coupled (careful)

These need coordination:

- Removing a column (code must stop writing it first).
- Renaming a column (use expand-contract).
- Changing a type (use a new column).
- Adding a NOT NULL constraint to existing column (requires backfill
  first).

#### Renaming a column safely

1. Add `new_name` column (nullable).
2. Code dual-writes both `old_name` and `new_name`.
3. Backfill `new_name` from `old_name`.
4. Code reads from `new_name`.
5. Code stops writing `old_name`.
6. Drop `old_name`.

Six steps. Each individually safe.

### Tools

- `Alembic` (Python).
- `Flyway`, `Liquibase` (Java).
- `migrate` (Go).
- `Active Record migrations` (Rails).
- `sqlx-migrate`, `refinery` (Rust).
- `Prisma migrate`, `Drizzle` (TS).

Whatever your tool, learn to use it. Don't run raw SQL on production.

## API Migrations

### Add new endpoint

```
GET /v1/users      # old
GET /v2/users      # new, different response shape
```

Both work. Clients gradually move.

### Add response field

```json
// before
{"id": "123", "name": "Alice"}

// after
{"id": "123", "name": "Alice", "createdAt": "..."}
```

Old clients ignore the new field. Backwards compatible.

### Remove response field

Risky. Old clients may depend on it.

- Add deprecation header.
- Document the change.
- Wait for client migration.
- Then remove.

### Change request shape

Hard. Use a new endpoint:

```
POST /v1/orders  → old shape
POST /v2/orders  → new shape
```

Coexist for a while; deprecate v1.

## Client / Backend Coordination

When client and backend must both change:

### Strategy 1: Client tolerates both

Server adds new behavior; client uses new if available, else old.

```javascript
const result = response.newField ?? deriveOld(response);
```

Server can ship first.

### Strategy 2: Server supports both

Server accepts old and new shapes; clients can be either.

```python
def parse_request(body):
    if "new_field" in body:
        return new_shape(body)
    return old_shape(body)
```

Client can ship anytime.

### Strategy 3: Feature flag

Both sides check the same flag. Coordinate the flip.

See [feature-flags.md](feature-flags.md).

## Data Migrations

Moving data from one shape to another:

### One-shot

Run a script that updates everything. Fast for small data.

```sql
UPDATE users SET email = LOWER(email) WHERE email != LOWER(email);
```

Risks:
- Long-running query holds locks.
- Failure leaves partial state.
- Large tables can't be done in one transaction.

### Chunked

Process in batches:

```python
while True:
    batch = db.fetch("SELECT id FROM users WHERE migrated = false LIMIT 1000")
    if not batch:
        break
    for row in batch:
        migrate_one(row)
    db.commit()
```

- Resumable on failure.
- Doesn't hold long locks.
- Can throttle to avoid load.

### Background job

Schedule a job that processes records over time. Useful for very large
datasets or live-traffic-sensitive cases.

### On-read migration

Code reads old format; if found, converts to new format and writes.

```python
def get_user(id):
    row = db.get(id)
    if row.format == "v1":
        row = convert_to_v2(row)
        db.save(row)
    return row
```

Slow but zero-downtime. Backfill a "sweep" job to migrate cold data.

## Multi-Service Migrations

When N services depend on each other:

### Reverse-dependency order

- Service A depends on B which depends on C.
- Migrate C first (it has no upstream dependencies).
- Then B.
- Then A.

### Backwards-then-forwards

If you can't do reverse-order:

- Make upstream tolerate both old and new downstream behavior.
- Migrate downstream.
- Tighten upstream.

### Versioned interfaces

RPC services with multi-version support:

- Service C exposes v1 and v2 endpoints.
- Service B migrates from v1 to v2.
- Service A migrates.
- Service C deprecates v1.

## File / Code Migrations

Moving files in a codebase:

- Use `git mv` (preserves history).
- Update imports atomically.
- Use codemod tools if many files:

```bash
# jscodeshift (JS/TS)
jscodeshift -t rename-old-to-new.js src/

# rust-analyzer batch rename
# (via editor command)

# Python: rope, libcst
# Go: gorename, gofmt -r
```

Mechanical changes are reviewable in one PR; cap the size.

## Migration Risks

### Data loss

A wrong migration can destroy data. Always:

- **Take backups.**
- **Test on staging.**
- **Do dry-runs.**
- **Have rollback plan.**

### Lock contention

A long migration on a busy table can block production.

- Use online schema change tools (gh-ost, pt-online-schema-change).
- Run during off-peak.
- Throttle.

### Partial state

A migration that runs partially leaves inconsistency.

- Make every step idempotent.
- Track progress (which records done).
- Have a "resume" path.

### Coordination failures

In distributed systems, things deploy out of order.

- Make migrations backwards compatible (old code can still read).
- Use feature flags to coordinate.
- Roll out gradually.

## Communication

For migrations affecting users:

- **Pre-announce.** Email, release notes, blog.
- **Migration guide.** Step-by-step.
- **Tools.** Provide a codemod, a script, an upgrade command.
- **Support period.** Be available for questions.
- **Post-announce.** "Migration complete; here's what changed."

The clearer, the smoother.

## Roll-Back Plans

Every migration should have a roll-back:

- Schema: previous schema kept compatible during deprecation.
- Data: backup + restore.
- Code: revert the PR.
- Services: previous version still available.

If you can't roll back, you can't migrate safely.

## Migration Anti-Patterns

### "Big bang"

Stop the system; convert everything; start. Fragile.

### Forgetting backwards compatibility

The new system works perfectly. The old client breaks. Production
goes down.

### No verification

Migration runs; no one checks the result. Subtle corruption goes
unnoticed for weeks.

Always: count records before and after, sample-check, log
discrepancies.

### "Just run it once"

A migration that can't be re-run is fragile. Make idempotent.

### Ignoring data growth

A migration that works on 10k rows takes hours on 10M.

Test at production scale (or production-like) before running.

## See Also

- [deprecation.md](deprecation.md)
- [feature-flags.md](feature-flags.md)
- [../05-fixing-issues/backwards-compatibility.md](../05-fixing-issues/backwards-compatibility.md)
- [../14-advanced/working-in-distributed-systems.md](../14-advanced/working-in-distributed-systems.md)
