# Onboarding Others

At some point you stop being the newcomer and become the person who
helps newcomers. Mentoring well is high-leverage — you multiply
yourself — and it's a skill, not just seniority. The empathy you needed
when *you* were new is the raw material.

## Why This Is Leverage

Helping others isn't charity that slows you down; it's force
multiplication:

- A person you onboard well becomes a productive contributor who lightens
  *your* load (and the maintainer's — see
  [../13-hidden-knowledge/burnout.md](../13-hidden-knowledge/burnout.md)).
- Teaching forces you to understand things deeply enough to explain them
  — you learn by teaching.
- It grows the bus factor: more people who understand the system means
  less fragility and less on you.
- It compounds: people you mentor go on to mentor others.

## Remember What Being New Felt Like

The core skill is fighting the **curse of knowledge** — once you know
something, you can't easily recall not knowing it, so you skip the steps
that would actually help. Deliberately recall the newcomer experience
(see [../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md)):

- The setup steps that were missing or assumed.
- The jargon used without definition.
- The "obvious" context that wasn't obvious.
- The fear of asking a "dumb" question.

Everything good about onboarding flows from remembering this.

## Writing First-Contribution Docs

The highest-leverage onboarding artifact: docs that get someone from
zero to their first merged change. These scale — written once, they help
everyone, even when you're not around.

A good first-contribution path includes:

- **Setup that actually works**, tested by someone new (the curse of
  knowledge makes *you* a bad tester of your own setup docs — watch a
  newcomer do it). See [../13-hidden-knowledge/docs-as-contribution.md](../13-hidden-knowledge/docs-as-contribution.md).
- **A "good first issue" trail** — curated, genuinely small, well-scoped
  starter tasks with enough context to start.
- **The contribution workflow** spelled out: branch, test, PR, review
  (link to your [../06-contribution/](../06-contribution/) equivalents).
- **Where to ask** — the channel, and explicit permission to ask.

Pair this with the newcomer's own friction log (see
[../13-hidden-knowledge/docs-as-contribution.md](../13-hidden-knowledge/docs-as-contribution.md))
— their stumbles are your doc backlog.

## Pairing Protocols

Pairing (working together in real time) is the highest-bandwidth
teaching tool, used well:

### Driver / navigator

The classic model: one person **drives** (types), the other
**navigates** (thinks ahead, spots issues, suggests direction). When
teaching:

- **Let the learner drive.** It's tempting to grab the keyboard, but the
  person typing is the person learning. You navigate.
- **Resist taking over.** Watching someone struggle for two minutes
  teaches more than you solving it in ten seconds. Intervene when
  they're stuck *and* frustrated, not at the first wobble.
- **Narrate your reasoning** when you do drive — the *why*, not just the
  keystrokes. The thought process is the lesson.

### Tactics

- **Ask, don't tell.** "What do you think happens here?" builds their
  model; "it does X" just transfers a fact.
- **Set a goal for the session** so it doesn't wander.
- **Keep sessions bounded.** Pairing is intense; an hour or two, then
  break.
- **Leave them something to do solo.** Independent practice consolidates
  what pairing taught.

## Calibrate to the Person

Mentoring isn't one protocol:

- **Match support to skill.** A true beginner needs more structure and
  encouragement; an experienced dev new to *this* domain needs context
  and pointers, not basics.
- **Find their edge.** Tasks just beyond current ability (with support)
  grow them; too-easy bores, too-hard demoralizes.
- **Adjust as they grow.** Pull back the scaffolding over time — the goal
  is independence, not dependence on you.

## Create Psychological Safety

People learn when it's safe to be wrong and to ask:

- **Normalize not-knowing.** "Great question, this confuses everyone" /
  "I had to look that up too." Share your *own* confusion and mistakes
  (see [../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md)).
- **Never punish a question.** A dismissive reaction once teaches them to
  stop asking — and then they stay stuck silently, which is worse for
  everyone.
- **Praise the process,** not just results. "Good debugging approach"
  reinforces the *method*.
- **Review kindly.** Their early PRs set whether they come back; the
  giving-review skills apply doubly to newcomers (see
  [giving-code-review.md](giving-code-review.md)).

## The Goal Is Independence

You're succeeding when they need you *less* over time. Mentoring that
creates dependence ("ask me every time") fails; mentoring that builds
judgment ("here's how to figure it out yourself") succeeds. Teach the
*how to learn*, point at the resources (see
[../09-unknown-tech/](../09-unknown-tech/)), and let go progressively.

## Anti-Patterns

### Curse of knowledge

Explaining from your expert vantage, skipping the steps a newcomer
actually needs. Remember what being new felt like; test docs on real
newcomers.

### Grabbing the keyboard

Solving it yourself because watching them struggle is uncomfortable. The
person typing is the person learning — let them drive.

### Just-tell-them

Handing over answers instead of building their model. Ask questions; let
them reach it. Faster for you, useless for them.

### Punishing questions

Any reaction that makes asking feel unsafe. They'll stop asking and stay
silently stuck — far costlier than the question.

### Creating dependence

Making yourself the required gateway for everything. Aim for their
independence, not your indispensability.

## See Also

- [building-reputation.md](building-reputation.md)
- [giving-code-review.md](giving-code-review.md)
- [../12-mindset/imposter-syndrome.md](../12-mindset/imposter-syndrome.md)
- [../13-hidden-knowledge/docs-as-contribution.md](../13-hidden-knowledge/docs-as-contribution.md)
- [../09-unknown-tech/learning-paths.md](../09-unknown-tech/learning-paths.md)
