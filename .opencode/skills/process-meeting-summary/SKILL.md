---
name: process-meeting-summary
description: >
  Process Zoom meeting transcripts into structured Obsidian markdown notes and
  place them in the correct vault location. Use this skill whenever the user
  mentions a Zoom transcript, meeting transcript, call recording, meeting notes
  they need to organize, raw transcript text they paste or share, or any file
  that looks like a Zoom or meeting transcript (speaker names + timestamps).
  Also use it when they say things like "clean up this transcript", "turn this
  into a meeting note", "process this meeting", "summarize this call", "save
  this to obsidian", or "put this in my vault". If the user shares a wall of
  text that has speaker names and timestamps, that's a transcript — use this
  skill. If they have a Zoom AI summary or auto-transcript, apply this skill.
  This skill is specifically for the user's Obsidian vault at:
  /mnt/c/Users/m.dupuis/OneDrive - TechSmith Corporation/Documents/Obsidian/Bag Of Holding
---

# Process Meeting Summary Skill

Process raw Zoom meeting transcripts (speaker + timestamp format) or Zoom AI
summaries into structured Obsidian markdown notes and route them to the correct
location in the user's vault. This is for the Obsidian vault at:

**Vault path:** `/mnt/c/Users/m.dupuis/OneDrive - TechSmith Corporation/Documents/Obsidian/Bag Of Holding`

## Input

Accept the transcript in one of these ways:

- **Pasted content**: The user pastes raw transcript text directly

### Raw Zoom Transcript Format

```
Speaker Name
HH:MM:SS
Transcript text...

Speaker Name
HH:MM:SS
More transcript text...
```

### Zoom AI Summary Format

```
Meeting Title: [Title]
Date: [Date and Time]
Duration: [X minutes]
Attendees: [List of attendees]

Summary:
[AI-generated summary]

Key Points:
- Point 1

Action Items:
- Item 1
```

Both formats are supported. Raw transcripts are preferred for preserving full
context, but Zoom AI summaries are common as a starting point.

## Workflow

1. **Accept input** — accept pasted transcript content
2. **Parse metadata** — extract date, title, attendees, duration. If the date isn't clear from the transcript, ask the user.
3. **Identify meeting type** — determine if it's a 1-1, project/strategy
   meeting, leadership/team meeting, or something else
4. **Route to Inbox** — all processed meeting notes go to the `Inbox/` folder
5. **Format the note** following the vault's established conventions
6. **Extract and categorize action items** — CRITICAL: Split action items into two separate sections:
   - **My Action Items** (with checkboxes `- [ ]`) for items owned by [[ME]] - do NOT include [[ME]] tag in the text
   - **Other Action Items** (plain bullets `-`) for items owned by anyone else - include owner wiki-link like [[Person Name]]
   - If there are no items for a section, omit that section entirely
7. **Write the file** to `Inbox/`
8. **Verify** the file was created correctly

## Vault Conventions

### Self-reference

Always use `[[ME]]` for Matt Dupuis (the user). Never use `[[Matt Dupuis]]`,
`[[mdupuis]]`, or any other variant. The file `ME.md` is at the vault root and
functions as the user's dashboard/personal page.

### Meeting Type Detection

#### 1-1 Meetings
- **Criteria**: 2 attendees total, one is [[ME]]
- **Destination**: `Inbox/`
- **File name**: `{YYYY-MM-DD} {Meeting Description}.md`
  (e.g., `2026-05-04 Wade Stevens 1-1.md`)
- **Title format**: `# {Person Name} 1-1 - {YYYY-MM-DD}` or similar descriptive title

#### Project/Strategy Meetings
- **Criteria**: Meeting title mentions a project name, strategy, planning,
  portfolio, roadmap, or cross-functional initiative
- **Destination**: `Inbox/`
- **File name**: `{YYYY-MM-DD} {Meeting Title}.md`

#### Team/Leadership Meetings
- **Criteria**: Multiple managers/leaders, recurring team syncs, department
  meetings, roundtables
- **Destination**: `Inbox/`
- **File name**: `{YYYY-MM-DD} {Meeting Title}.md`

#### Unknown / Can't Classify
- **Destination**: `Inbox/` for manual review
- **File name**: `{YYYY-MM-DD} {Title}.md`
- Notify the user about the routing decision so they can move it

### Note Format

Follow this structure, modeled on the existing notes in the vault:

```markdown
---
title: "{Meeting Title}"
date: {YYYY-MM-DD}
meeting_type: "{1-1 / Project/Strategy / Team/Leadership / Unknown}"
attendees:
  - "[[Person 1]]"
  - "[[Person 2]]"
tags:
  - meeting
  - {tag1}
  - {tag2}
---

# {Descriptive Title} - {YYYY-MM-DD}

**Attendees:** [[ME]], [[Person 1]], [[Person 2]]

**Duration:** {X minutes}

**Summary:**
{2-4 sentence summary covering what was discussed and key outcomes}

**Key Points:**
- **{Topic Label}** — {concise description of what was discussed or decided}
  > Person: "Direct quote if exact wording adds value"

**My Action Items:**
- [ ] {action description} {priority} {📅 YYYY-MM-DD if due date mentioned}

**Other Action Items:**
- {action description} [[Owner]] {priority} {📅 YYYY-MM-DD if due date mentioned}

**Decisions:**
- {one decision per bullet}

---
```

A few important notes on formatting:

**YAML tags**: Generate 2-5 relevant tags from the meeting content. Always include
`meeting` as the first tag. Derive additional tags from key topics, project names,
people mentioned, teams, or technologies discussed. Use lowercase, hyphenated tags
(e.g., `platform-engineering`, `typescript`, `q3-planning`).

**Transcript link**: Omit the `**Transcript:**` line since transcripts are always pasted in — there's no source file to link to.

**Blockquotes**: Include selective verbatim quotes when they add value —
commitments, feedback, sensitive topics, specific guidance, expressions of
enthusiasm or concern, or when the nuance can't be captured in a summary.
Aim for 1-3 good quotes per meeting. Don't quote mundane conversational
filler like greetings or small talk.

**Action items**: Check if meeting discussion naturally produced action items.
Split action items into two sections:

1. **My Action Items** — for items owned by [[ME]]. Format as Obsidian checklist 
   items with checkboxes. Do not add `#task` tag. Do not include [[ME]] in the 
   item text since the section header makes ownership clear.

2. **Other Action Items** — for items owned by anyone else. Format as plain bullets 
   (no checkboxes). Owner is always specified as a wiki-link (e.g., [[Person Name]]).

For both sections, add `🔼 🔽 🔴 🟡 🟢` priority markers if urgency was discussed, 
and add `📅 YYYY-MM-DD` if a due date was mentioned. These are **potential** action 
items for manual review and tagging.

If there are no action items for a section, omit that section entirely rather than 
leaving it empty.

**Action item extraction**: When the speaker says "I'll do X" or "I'll follow up on Y",
capture that in the "My Action Items" section. When someone says "Can you do X?" to 
another person, capture it in "Other Action Items" with the appropriate owner. When 
it's ambiguous who owns the item, use your best judgment based on context, or default 
to "Other Action Items" with the most likely owner.

**Example of correct action item formatting:**

```markdown
**My Action Items:**
- [ ] Review the approach with Dorie this week 🔼
- [ ] Get back to Derek by Friday 📅 2026-06-12

**Other Action Items:**
- Send follow-up email on waiver [[Fatih Kaplan]] 📅 2026-06-09
- Create PRs for automation pipeline [[Tyler Colwell]]
- Review Brett's licensing pipeline [[Mark Turnpaugh]]
```

Notice: My Action Items use checkboxes and no [[ME]] tags. Other Action Items use plain bullets and include owner wiki-links.

## Writing Style

- **Summary + Selective Quotes** approach: structured summaries for scanability,
  verbatim quotes only when exact wording adds value
- Focus on **outcomes, decisions, and actions** rather than conversational flow
- Use **bold topic labels** followed by `—` in Key Points for scanability
- Keep summaries concise — the reader should get the gist in 30 seconds

## Raw Transcript File Handling

Since transcripts are always pasted in, no raw transcript file is saved. Just create the processed note in `Inbox/` and omit the `**Transcript:**` link.

## Person File Convention

Individual meeting notes go into `Inbox/`:
`Inbox/{YYYY-MM-DD} {description}.md`

Do NOT append to a single person file. Each meeting gets its own file.
