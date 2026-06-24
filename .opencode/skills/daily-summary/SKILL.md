---
name: daily-summary
description: Summarize all markdown files from a specific date into a concise daily brief with priorities, action items, and accomplishments. Use this skill whenever the user wants a summary of their day, asks "what happened today", "summarize my day", "what did I do on [date]", "daily recap", "give me a rundown of [date]", wants to review notes from a particular day, or asks about priorities or accomplishments from a date — even if they don't explicitly say "daily summary". Also use when the user provides a date and asks what's in their notes for that day.
---

# Daily Summary Skill

This skill finds all markdown files in the user's Obsidian vault whose filenames begin with a specific date (YYYY-MM-DD format), reads their content, and produces a concise summary optimized for quick scanning.

## Workflow

1. **Determine the date** — Use the date provided by the user. If none given, default to today's date.
2. **Find matching files** — Glob for `**/YYYY-MM-DD*.md` across the entire vault (the working directory).
3. **Read each file** — Load the content of every matching file.
4. **Synthesize a summary** — Produce a single cohesive daily brief following the output format below.
5. **Write the file** — Save the summary as a markdown file at `Areas/ME/Daily Summaries/YYYY-MM-DD Daily Summary.md` (relative to the vault root). Create the directory if it doesn't exist. Also display the summary in the conversation so the user can see it immediately.

## Output Format

The output should be markdown that's easy to copy into a file. Use this structure:

```markdown
---
date: YYYY-MM-DD
meetings:
  - "Meeting title 1"
  - "Meeting title 2"
tags:
  - tag1
  - tag2
---

# Daily Summary — YYYY-MM-DD

## Top Priorities
- Bulleted list of the most important/urgent items that need attention
- Derived from action items, deadlines, and context clues about urgency

## Action Items
- [ ] Task with owner and due date if known
- [ ] Another task
(See filtering rules below for which action items to include)
```

Replace the Action Items section above with this structure:

```markdown
## My Action Items

### 🔴 High Priority
- [ ] Items with immediate deadlines, strategic impact, or that unblock others
- [ ] Things only Matt can do, or where delay creates risk

### 🟡 Lower Priority
- [ ] Important but not urgent — can be done later this week or deferred
- [ ] Housekeeping, personal workflow improvements, low-stakes logistics

## Others' Action Items
- Task owned by someone else [[Owner Name]]
- Only include items Matt needs visibility into (direct reports, key dependencies)
```

Use your best judgment to split Matt's items by impact. High priority = time-sensitive (tomorrow's deadline), strategically important (PI planning, cross-team dependencies), or high leverage (unblocks others). Lower priority = nice-to-have, personal workflow, flexible timing.

## Accomplishments
- Things completed, decisions made, milestones reached
- Wins worth remembering

## Key Takeaways
- Concise bullets summarizing the most important information from across all files
- Favor brevity; each bullet should be one meaningful point
- Group related items if it aids clarity

## Blind Spots & Things You Might Be Missing
- Threads that were mentioned but not followed up on
- Commitments others made to you that lack a next step
- Risks or dependencies that could stall if not tracked
- Conversations where you deferred but no one else picked it up
(Be honest and direct here — this section is meant to surface what falls through cracks)

## Biggest Opportunities
- Where you could have outsized impact based on today's context
- Leverage points: things only you can unblock, or where small effort yields large results
- Growth opportunities: skills being stretched, visibility moments, or strategic positioning
(Think like a coach — what would you tell this person to double down on?)

## Files Included
- [[filename1]]
- [[filename2]]
```

## YAML Frontmatter

The summary file must begin with a YAML metadata block:

- **date** — The summary date in YYYY-MM-DD format.
- **meetings** — A list of meeting titles extracted from the source files. Derive these from filenames or H1 headings of files that are clearly meeting notes (look for patterns like "Meeting Name" or transcript-style content). Omit if no meetings occurred.
- **tags** — A list of topic tags derived from the summary content. Use short, lowercase, hyphenated tags that capture the key themes (e.g., `installer`, `auth0`, `ci-cd`, `hiring`, `1on1`, `pi-planning`). Aim for 3–7 tags that would help with future search and filtering.

## Guidelines

- **Be concise.** Bullet points over paragraphs. Each bullet should earn its place.
- **Prioritize ruthlessly.** The "Top Priorities" section is the most important — it should reflect what genuinely needs attention soonest, not just everything that was mentioned.
- **Accomplishments matter.** Actively look for things the user achieved, shipped, decided, or completed. These are easy to overlook but valuable for reflection.
- **Deduplicate.** The same action item may appear in multiple meeting notes. List it once.
- **Filter action items by relevance.** The action item list should be curated, not exhaustive. Apply this hierarchy:
  1. **Always include:** Items clearly assigned to or owned by Matt (the user). These are the core of the list.
  2. **Include most:** Items owned by Matt's direct reports (Paul Soma, Josh Schultz, Fatih Kaplan) — these are things Matt needs visibility into as their manager.
  3. **Include sparingly:** Items for other Platform pod members (e.g., Mark Turnpaugh, Tyler Colwell, Edvin Kuc, etc.) — only if they represent a meaningful risk, blocker, or something Matt should track.
  4. **Include only if high-impact:** Items for peer Dev Managers (Scott Schmerer, Derek Hammond, Tony Cooke, Jason Eagleston) — only cross-department commitments or dependencies that directly affect Platform.
  5. **Never include:** Action items for people outside Platform and outside the Dev Manager peer group. These are someone else's problem.
  
  The goal is a list Matt can scan in 30 seconds and know exactly what he needs to do and what his team is on the hook for — not a dump of every task mentioned in every meeting.
- **Preserve wiki-links.** Use `[[Person Name]]` formatting when referencing people or notes. Use `[[ME]]` for Matt Dupuis.
- **If no files are found**, tell the user clearly: "No files found for YYYY-MM-DD."
