# When You Can't Reproduce

You've tried for two hours. The bug clearly happened (logs, screenshots,
angry users) but you can't make it happen on your machine. Now what?

## First: Get More Information

Don't go heads-down on code. Go back to the reporter:

- **Exact version** they were running.
- **Exact environment** (OS, browser, locale).
- **Exact steps** they took.
- **Exact time** the issue happened (helps correlate with logs).
- **Any logs / screenshots** they have.

A 5-minute conversation often unlocks a 5-hour search.

## Look at Production

If the bug is in a deployed service, look there:

- **Production logs** at the time of the report.
- **Error tracking** (Sentry, Bugsnag, Rollbar) — they often capture
  variables and stack traces.
- **Metrics** — did something spike?
- **Recent deploys** — did anything change?

Production has the actual data the bug needed. Dev usually doesn't.

## Reproduce Against Production Data (Safely)

Sometimes you can:

- Anonymize the failing input and pull it locally.
- Use a database snapshot (with PII scrubbed).
- Replay a specific request from logs (`curl` with the same headers/body).

Often the bug becomes obvious once you have the right input.

Always anonymize. Don't store production PII locally beyond what's
necessary, and delete it after.

## Match Their Environment

Common gotchas:

- They're on Windows; you're on Mac.
- They're using a different browser.
- They have a slow network (timeouts kick in differently).
- Different timezone (date math).
- Different locale (number/string formatting).
- A specific OS version (kernel bug, API behavior change).

Try replicating: VMs, Docker, BrowserStack/Sauce Labs for browsers,
network throttling tools.

## Bisect by Configuration

If the bug is rare in production but you have many similar environments
that don't reproduce, bisect by **config**:

- Compare configs between a system that fails and one that doesn't.
- One difference at a time, test.
- Often a single setting (a feature flag, an env var) is the trigger.

## Look for Patterns in Reports

If multiple users report something similar:

- Are they all on the same version?
- Same OS?
- Same data shape (large orgs, specific industries)?
- Same time of day?

Patterns across many "I can't repro" reports often reveal the trigger.

## Make It Reproduce by Looking at Code

When environmental approaches fail, use code:

- Read the failing function carefully.
- Identify external inputs (function args, config, env, network).
- For each input, ask: "what specific value would make this fail?"
- Try that value.

Often the issue is an unguarded null, an empty list, a Unicode quirk,
a leap-second handling.

## Trace Without Reproducing

If you can't run the bug yourself:

- Add **more logging** in the suspect area.
- Add it cautiously — don't spam.
- Ship it; wait for the bug to recur.
- Now you have data.

This is slow (you wait for production) but often the only way.

## The "Defer to Production" Approach

For very-hard-to-reproduce bugs in services:

1. Add defensive logging at the suspected failure point.
2. Add a `panic` / `log.error` with full context if a known-bad
   condition is detected.
3. Deploy.
4. Wait.
5. When it recurs, you have full context.

This is a real strategy, not laziness. Some bugs only exist in
production data and have to be hunted there.

## "Cannot Reproduce" as a Resolution

Sometimes the answer is: this bug isn't reproducible, here's what I
tried.

Document:
- Exactly what you tried.
- Why you can't proceed without more info.
- What you'd need to investigate further.

Close the issue with a polite message:

> "Spent ~3h trying to reproduce; couldn't trigger with the input
> shapes I tried. If you encounter again, please include [X, Y, Z].
> Reopening if/when we have new info."

Don't keep an issue open forever; don't fake-close it as "fixed."

## The "Smell Test" — Was It Even a Bug?

Sometimes reports are based on misunderstanding. Verify:

- Is the behavior actually wrong, or unexpected-but-correct?
- Is the user describing a different system than yours?
- Is it user error (wrong config, wrong endpoint)?

Be polite when this is the case. "Got this fail" is a normal user
report; the user doesn't know it's their config.

## "Cannot Reproduce" Patterns You Should Recognize

### Cosmic ray / hardware flap

Truly random. One-time. Usually unfixable. If it happens twice, suspect
something else.

### Race condition

Timing-sensitive. May only fail under load or under specific
schedulers. See [heisenbugs.md](heisenbugs.md).

### Network blip

Lost packet, slow DNS, transient outage. Often "the system" recovered
without your help.

### Stale cache

The bug is the cache's fault. Reproduces with stale data but not fresh.
"Have you tried clearing your cache?" — often more diagnostic than dismissive.

### Configuration drift

Different config in production vs the reporter's system. Hard to find
unless you compare configs.

### Specific input

A particular Unicode character, very long string, very large number,
empty array. Hard to find without seeing the actual input.

### Concurrency

The bug requires two operations to interleave just so. Reproduces on
high-traffic systems, not on dev.

## Defensive Programming as Diagnosis

When you can't repro, sometimes the answer is to **harden** the
suspected area:

```python
def process(value):
    if value is None:
        log.error("process called with None — should not happen, see #4521")
        raise ValueError("None")
    ...
```

Now the bug will report itself with clear context. Reproduction not
needed.

## See Also

- [minimal-reproduction.md](minimal-reproduction.md) — the goal you're not reaching
- [heisenbugs.md](heisenbugs.md) — timing-sensitive sibling
- [observability.md](observability.md) — production data as substitute for repro
