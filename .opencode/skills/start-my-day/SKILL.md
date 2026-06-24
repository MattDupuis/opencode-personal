---
name: start-my-day
description: Generate a focused daily action plan based on yesterday's notes. Use this skill whenever the user wants to start their day, plan their morning, asks "what should I do today", "start my day", "morning plan", "what's on my plate", "plan my day", "daily kickoff", or mentions wanting to review what happened yesterday to figure out today's priorities — even if they don't explicitly say "start my day". Also use when the user opens a conversation first thing in the morning and asks what they should focus on.
---

# Start My Day

This skill reads yesterday's vault files and produces a forward-looking action plan for today — a concise markdown file saved to the `Areas/ME/Start My Day/` folder.

## Workflow

1. **Confirm the date** — Ask the user: "What's today's date? (I'll default to today if you just hit enter)" Use today's date from the environment as the default. Once confirmed, compute the "last workday" date: if today is Monday, use the previous Friday; otherwise use the day before.

2. **Find last workday's files** — Glob for `**/YYYY-MM-DD*.md` using the last workday's date across the entire vault. If today is Monday and no files are found for Friday, try Thursday, then Wednesday — weekends and PTO days may leave gaps. This captures:
   - The Daily Summary (`Daily Summaries/YYYY-MM-DD.md`)
   - The raw daily note (`Notes/Daily/YYYY-MM-DD.md`)
   - Any meeting notes with yesterday's date in the filename

3. **Read each file** — Load the content of every matching file.

4. **Synthesize today's action plan** — Analyze everything from yesterday and produce a focused, forward-looking plan. Think about:
   - What was left undone or unresolved?
   - What commitments did Matt or his team make that need follow-up today?
   - What meetings or deadlines are coming up that need prep?
   - What strategic threads deserve attention before they go cold?
   - What quick wins are available?

5. **Write the file** — Save as `Areas/ME/Start My Day/YYYY-MM-DD Start My Day.md` (using today's date) relative to the vault root. Also display the contents in the conversation.

## Output Format

```markdown
---
title: "Start My Day - YYYY-MM-DD"
date: YYYY-MM-DD
tags:
  - daily-plan
  - {tag1}
  - {tag2}
---

# Start My Day - YYYY-MM-DD

## Do First (High Priority)
- [ ] Concrete action items that are time-sensitive or blocking others
- [ ] Things with deadlines today or dependencies waiting on Matt

## Follow Up
- [ ] Check in on commitments others made yesterday
- [ ] Items that need a nudge or status check
- [ ] Threads that will go cold if not touched today

## Strategic Reminders
- Bigger-picture things to keep in mind during conversations
- Opportunities spotted yesterday worth acting on
- Context that reframes today's meetings or decisions

## Prep Needed
- [ ] Meetings or conversations coming up that need thought beforehand
- [ ] Materials to review, decisions to make before a session

## Quick Wins
- [ ] Small things that take <5 minutes and clear mental load
- [ ] Replies, approvals, or acknowledgments that are overdue
```

## Guidelines

- **Be concrete.** Every action item should be specific enough to act on without re-reading yesterday's notes. "Follow up with Josh" is too vague — "Ask Josh for the SSO migration timeline he mentioned in standup" is actionable.
- **Prioritize ruthlessly.** "Do First" should have 2-4 items max. If everything is high priority, nothing is.
- **Keep it scannable.** The whole file should take under 60 seconds to read. Aim for 15-25 total items across all sections.
- **Preserve wiki-links.** Use `[[Person Name]]` for people and `[[ME]]` for Matt.
- **Don't repeat the daily summary.** This isn't a recap — it's a plan. Frame everything as forward-looking actions, not backward-looking descriptions.
- **Use judgment on sections.** If there's nothing for "Quick Wins" or "Prep Needed", leave that section out rather than padding it with filler.
- **YAML tags.** Always include `daily-plan` as the first tag. Derive 2-5 additional tags from the key themes, projects, or people featured in the plan. Use lowercase, hyphenated tags (e.g., `pi-planning`, `modular-installer`, `bonus-activities`).
- **If no files are found** for yesterday, tell the user: "No files found for yesterday (YYYY-MM-DD). Want me to try a different date?"

## Files Included Section

At the bottom, add a collapsed section listing what was read:

```markdown
---
<details>
<summary>Source files</summary>

- [[filename1]]
- [[filename2]]

</details>
```
