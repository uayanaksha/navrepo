# The Five Whys

A debugging technique borrowed from manufacturing (Toyota). Ask "why?"
repeatedly until you hit a root cause that suggests a fix at the right
level.

## The Method

For each level of "why," the answer suggests a possible fix. The
*right* fix is usually at level 4 or 5 — not level 1.

### Example: Service crashed under load

> **Why did the service crash?**
> Because it ran out of file descriptors.

A fix here: raise the ulimit. (Band-aid.)

> **Why did it run out of FDs?**
> Because the HTTP client was leaking connections.

A fix here: explicitly close client connections. (Closer.)

> **Why were connections being leaked?**
> Because exceptions in the response-handling path skipped the cleanup.

A fix here: use `try/finally` or context managers. (Getting warm.)

> **Why were exceptions skipping cleanup?**
> Because the cleanup was after the response parse, which threw on
> malformed responses.

A fix here: cleanup before parse, or in `finally`. (Hot.)

> **Why was the response sometimes malformed?**
> Because upstream service was returning 502 with HTML body when
> rate-limited; we expected JSON.

A fix here:
- Handle 502 explicitly.
- Add a fallback path for non-JSON responses.
- Reduce request rate to avoid 502 in the first place.

Each level reveals new fix options. The lowest level is often the
*best* fix.

## When to Stop

You don't always go to five. Stop when:

- The next "why" leaves your scope (e.g., "why does the upstream return
  502?" might be out of your control).
- You've reached something fundamental that can't change (e.g., "why
  does TCP work this way?").
- You've identified the right place to intervene.

Five is a guideline, not a rule.

## The Lateral Why

Sometimes "why" branches:

> Service crashed.
> ├── Why? Out of FDs.
> ├── Why? Connections leaked.
> ├── Why? Exceptions in handler.
> │   ├── Why? Malformed responses from upstream.
> │   └── Why? Race condition in our client (separate bug).

Both branches are real. Both might need fixes. Sometimes the *primary*
bug is one branch, and the other is a finding to file separately.

## "Why" vs "How"

Don't conflate:
- **Why** asks for cause: "Why did X happen?"
- **How** asks for mechanism: "How did X manage to happen?"

In debugging, you usually want *why*. "How" is mechanical and shallow;
"why" surfaces design issues.

## Process / People / Code

Five Whys works at multiple levels:

### Code
"Why did the function fail?" → mechanism in code.

### Process
"Why did this bug get into production?"
→ Why wasn't it caught in code review?
→ Why didn't tests cover it?
→ Why is this code path under-tested?
→ Why is testing this area hard?
→ Why is the design hard to test?

Now you have a refactor proposal *and* a test improvement.

### People
"Why didn't anyone notice the deploy was broken for 3 hours?"
→ Why didn't on-call get paged?
→ Why didn't the alert fire?
→ Why is the alert threshold wrong?
→ Why was the threshold set without considering this case?
→ Why is alert-setting ad-hoc?

The lowest level is "alert-setting needs a clearer process." A process
fix, not a code fix.

## Asking "Why" in PRs and Postmortems

When discussing a fix:

> "Adding null check here."

vs:

> "Adding null check because the upstream `get_user()` can return null
> when the user is mid-deletion. Root cause is a race between
> deletion and our cache invalidation, tracked in #N. This is a
> patch; #N has the real fix."

The second has all the why-levels visible. Future readers can verify
or refute.

## Five Whys Anti-Patterns

### Stopping at "human error"

> Why did the deploy fail? Operator typed wrong command.

This is too early. Keep going:
> Why did the operator type the wrong command? The doc was ambiguous.
> Why? Two valid sequences for the same operation.
> Why? Different tools used historically without unification.

Now you have a process improvement, not a person to blame.

### Finger-pointing

Five Whys is a debugging tool, not a tribunal. If the first "why"
identifies a person, redirect to the *system* that allowed the
person's choice to cause the problem.

### Stopping at "we forgot"

> Why didn't we catch it in code review? Reviewer missed it.

Keep going:
> Why? The change spanned 1500 lines.
> Why? The review process accepts large PRs.
> Why? We don't enforce PR size guidance.

Process improvement: enforce PR size.

## When Not to Use Five Whys

For trivial bugs (typo, off-by-one), Five Whys is overkill. Fix and
move on.

For systemic / cultural issues, Five Whys is a starting point but not
sufficient. Conversations and design work follow.

## The 5-Whys Worksheet

When investigating something significant:

```
Problem: <one-sentence>

Why 1: <answer>
  Fix at this level: <possible fix>
  Verdict: too superficial / accept

Why 2: <answer>
  Fix at this level: <possible fix>
  Verdict: too superficial / accept

Why 3: ...

Root cause: <statement>
Chosen fix level: <level + reason>
Other fixes deferred to: <issue ID>
```

This is great content for PR descriptions and postmortems.

## See Also

- [root-cause-vs-symptom.md](root-cause-vs-symptom.md) — the framing
- [../14-advanced/incident-response.md](../14-advanced/incident-response.md) — five-whys in postmortems
