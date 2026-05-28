# Building Reputation

Reputation in a community or a company is the accumulated answer to one
question: *can people rely on you?* It's built slowly, over years, in
small consistent acts — and it's the thing that eventually lets your
ideas get taken seriously and your big changes get trusted.

## What Reputation Actually Is

It's not fame or commit count. It's **trust**, specifically:

- Your code is correct and considerate, so reviews go faster.
- Your judgment is sound, so your opinions carry weight.
- You follow through, so people give you bigger things.
- You're good to work with, so people *want* to work with you.

This is the same "trust" that's the fifth force in the maintainer's
calculus (see [../13-hidden-knowledge/maintainer-calculus.md](../13-hidden-knowledge/maintainer-calculus.md)).
Reputation is that trust, accumulated and generalized.

## It's a Multi-Year Arc

There's no shortcut. Reputation compounds like the reading habit (see
[../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md)):

```
Month 1:   A few good docs PRs.            → "this person is careful"
Month 3:   Solid small fixes, good repros. → "this person is reliable"
Month 6:   A real feature, well-executed.  → "this person is capable"
Year 1:    Reviewing others, triaging.     → "this person is invested"
Year 2+:   Trusted with direction, maybe   → "this person is one of us"
           commit access / maintainership.
```

Each rung is *earned* by the previous one. You can't skip to "trusted
with direction"; you get there by being reliable a hundred times first.
The trust ladder in [../13-hidden-knowledge/governance.md](../13-hidden-knowledge/governance.md)
is the formal version of this.

## What Builds It

### Consistent quality

The foundation. Every scoped PR, every good bug report, every tested
change is a small deposit. Reputation is the *integral* of your work
over time — consistency beats occasional brilliance. One reliable
contributor is worth more than one sporadically dazzling one.

### Following through

Do what you said you'd do. Finish the PR you started. Respond to the
review. Don't leave things half-done. "This person follows through" is
one of the most valuable things people can believe about you — and one
abandoned commitment dents it.

### Reciprocity

Communities run on give-and-take. The people with the best reputations
*give*: they review others' PRs, answer questions, reproduce bugs,
mentor newcomers (see [onboarding-others.md](onboarding-others.md)).
Giving — especially the unglamorous triage and review work — builds
reputation faster than just shipping your own features, because it
helps the people whose opinion of you matters.

### Being easy to work with

The under-rated multiplier. Taking feedback gracefully (see
[../12-mindset/receiving-review.md](../12-mindset/receiving-review.md)),
disagreeing respectfully (see [../08-maintainers/disagreement.md](../08-maintainers/disagreement.md)),
accepting "no" well (see [../13-hidden-knowledge/saying-no.md](../13-hidden-knowledge/saying-no.md)),
and being patient (see [../13-hidden-knowledge/time-zones.md](../13-hidden-knowledge/time-zones.md)).
People extend trust to those who are pleasant to collaborate with — and
withhold it from the brilliant-but-difficult.

### Honesty and calibration

Saying "I'm not sure" when you're not, and "I was wrong" when you were
(see [../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md)).
People trust those whose confidence tracks reality. One confident wrong
assertion costs more credibility than ten honest uncertainties.

## What Destroys It (Faster Than You'd Think)

Reputation is asymmetric — slow to build, fast to lose:

- **Carelessness that bites others.** A sloppy change that breaks things,
  especially if you were warned.
- **Not following through.** Abandoning work, ghosting reviews.
- **Being difficult.** Defensiveness, arrogance, relitigating every
  "no," treating people as obstacles.
- **Dishonesty.** Overstating what you did, hiding what you broke,
  claiming others' work. The fastest reputation-killer; often
  unrecoverable.
- **Entitlement toward maintainers.** Treating volunteers as your support
  desk (see [../12-mindset/be-the-contributor.md](../12-mindset/be-the-contributor.md)).

A single instance of any of these can undo months of deposits. Guard the
account.

## Optional Amplifiers: Speaking and Writing

Beyond the work itself, communicating it broadens reputation:

- **Writing** — a clear blog post, a good design doc, helpful answers —
  reaches people you'll never meet and demonstrates your thinking.
- **Speaking** — talks and meetups (the flip side of mining them in
  [../13-hidden-knowledge/conference-talks.md](../13-hidden-knowledge/conference-talks.md)).
- **Teaching** — onboarding docs, mentoring, tutorials.

These *amplify* a reputation but don't *substitute* for the underlying
reliability. A great talk from someone whose PRs are sloppy rings
hollow. Do the work first; then tell people about it.

## Reputation Is Portable — and Permanent

Two things to internalize:

- **It follows you.** Across projects, jobs, and years, the developer
  community is smaller than it looks. The reputation you build (good or
  bad) travels.
- **It's mostly public and durable.** Your PRs, issues, reviews, and
  comments are a permanent, searchable record. Act as though the person
  reading your old comment is a future collaborator deciding whether to
  trust you — because they are.

## Anti-Patterns

### Optimizing for visibility over substance

Chasing commit counts, comment volume, or stars instead of being
reliable (see [../12-mindset/activity-vs-progress.md](../12-mindset/activity-vs-progress.md)).
Hollow metrics don't build trust; consistent quality does.

### Expecting fast returns

Treating reputation as something you can grind out in a month. It's a
multi-year integral. Be patient and consistent.

### Taking it for granted once built

Resting on a good reputation and getting sloppy or difficult. The
account drains fast; keep depositing.

### Brilliance without reliability

Being occasionally dazzling but unpredictable, defensive, or
flaky. People trust the reliable over the brilliant-but-difficult every
time.

### Burning a bridge over one disagreement

Torching a relationship because one "no" stung. The community is small
and long-lived; today's reviewer is tomorrow's reference.

## See Also

- [onboarding-others.md](onboarding-others.md)
- [../12-mindset/activity-vs-progress.md](../12-mindset/activity-vs-progress.md)
- [../12-mindset/be-the-contributor.md](../12-mindset/be-the-contributor.md)
- [../13-hidden-knowledge/maintainer-calculus.md](../13-hidden-knowledge/maintainer-calculus.md)
- [../13-hidden-knowledge/compounding-reading.md](../13-hidden-knowledge/compounding-reading.md)
