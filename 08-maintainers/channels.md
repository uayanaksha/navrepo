# Communication Channels

Where you say it matters as much as what you say. Picking the right
channel for the right message reduces friction and respects everyone's
time.

## The Channels

| Channel | Latency | Permanence | Visibility |
|---|---|---|---|
| Issue / PR comment | Hours to days | Permanent | Public |
| Discussions | Hours to days | Permanent | Public |
| Discord / Slack | Minutes | Searchable but transient | Semi-public |
| Mailing list | Days | Archived | Public |
| Email | Days | Private | Private |
| DM (Twitter, Slack, etc.) | Variable | Often lost | Private |
| Conference / call | Real-time | Often unrecorded | Private |

Most decisions should be in **permanent + public** channels.

## When to Use Each

### Issue / PR comments

**Default for**:
- Anything decision-relevant.
- Status updates on a specific change.
- Technical discussion of code.

Anyone reading the issue/PR later sees the full context. This is the
single most important channel.

### Discussions (GitHub feature)

**Use for**:
- Open-ended ideas not ready for an issue.
- Q&A that doesn't fit "I have a bug."
- Polls or feedback collection.

Cleaner than issues for non-actionable conversations. Doesn't clutter
the issue tracker.

### Discord / Slack / Matrix

**Use for**:
- Quick questions ("anyone know if X is supported?").
- Real-time troubleshooting.
- Community presence.
- Sync coordination.

**Don't use for**:
- Substantive decisions (won't be searchable later).
- Detailed technical proposals.
- Anything that should be in the record.

If a decision is made in chat, restate it in an issue/PR.

### Mailing list

**Use for** (when project uses):
- Long-form proposals (Linux, Postgres, Ruby).
- Patches via email (Linux kernel still works this way).
- Community-wide announcements.

Read the list's archive to understand its norms before posting.

### Email (direct)

**Use for**:
- Security disclosure (per `SECURITY.md`).
- Coordinating timing privately ("can you review this Wednesday?").
- Anything genuinely personal.

**Don't use for** substantive technical or process discussion.

### DMs

**Use for**:
- Personal context ("congrats on your release").
- Quick coordination ("are you available now?").

**Don't use for**:
- Code review.
- Decision-making.
- Anything you'd want in the record.

### Conference / call

**Use for**:
- Complex design conversations.
- Relationship-building.
- Onboarding.

After: **write up the conclusions** somewhere permanent.

## Decision Channel Hierarchy

For decisions that affect the project:

1. **PR / issue comment** — preferred.
2. **Mailing list** — if project lives there.
3. **Public Discussion / forum thread** — for broader topics.
4. Anything else needs to be **summarized back** to permanent public
   record.

Decisions made in private chat that never make it back to public
record are invisible to future contributors. This is bad for the
project.

## "I Can DM You About This?"

When someone wants to take a public conversation private:

- If it's logistics (timing, schedule), DM is fine.
- If it's substantive (technical, design), gently push back to public:

> "Happy to chat — could we keep substantive parts in the PR so the
> context is preserved? Otherwise DM works."

Usually they understand.

## "I'll Talk to You on Discord"

If a maintainer says they'll engage on Discord:

- Use it.
- After: post a summary in the PR/issue.

> "Sync update — Alice and I discussed in Discord. Plan:
> 1. ...
> 2. ...
> Will continue here."

Restate; don't replace.

## Private Channels Have a Place

Some things genuinely belong in private:

- Security vulnerabilities.
- Personal conflict resolution.
- Compensation / financial.
- HR-style issues.

For these: follow `SECURITY.md` for security. Use direct email /
appropriate maintainer for others.

## When You're Stuck Between Channels

You're not sure if you should issue, PR, or chat. Defaults:

- **Bug report**: issue.
- **Feature proposal**: discussion or issue.
- **Quick question**: chat.
- **Already have a fix**: PR.

When in doubt: issue. Issues can be re-routed; chat messages can't.

## Channel-Specific Etiquette

### Issue / PR

- One topic per issue.
- Use markdown / code blocks.
- Quote what you're responding to.
- Use thread comments for sub-discussions where supported (GitLab,
  Linear).

### Discord / Slack

- Don't @here unless emergency.
- Use threads if the platform supports.
- Don't ask if you can ask; just ask.
- Don't double-post the same question across channels.

### Mailing list

- Bottom-post, not top-post (most lists' convention).
- Trim quoted text.
- Use plain text, not HTML.
- Respect reply-all etiquette.

### Conference calls

- Camera optional; presence not.
- Take notes; share them.
- Don't dominate.

## Project Channel Discovery

Find them via:

- README ("Community" or "Discussion" section).
- CONTRIBUTING.md.
- Repo's profile / sidebar.
- A `chat.md` or similar file.

Some projects also have:

- Monthly community calls.
- Office hours.
- Conferences / meetups.

Calendar these if you're invested.

## Multiple Channels Per Project

Many projects have multiple channels:

- GitHub for issues / PRs.
- Discord for community chat.
- Twitter / Mastodon for announcements.
- Mailing list for major decisions.

This is fine. Just keep substantive decisions in the searchable
record.

## Signal-to-Noise

The more channels exist, the more noise. As a contributor, you don't
have to follow all of them.

Priority:
1. Your active PR / issue notifications.
2. CHANGELOG / release notes.
3. Optional: Discord for community feel.
4. Optional: Twitter for announcements.

Don't FOMO yourself into following everything.

## See Also

- [postures.md](postures.md) — tone applies across all channels
- [ping-etiquette.md](ping-etiquette.md)
- [../13-hidden-knowledge/](../13-hidden-knowledge/) — backchannel dynamics
