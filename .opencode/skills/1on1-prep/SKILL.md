---
name: 1on1-prep
description: Prepare for a 1-on-1 meeting with Dorie Blaisdell (Matt's manager). Use this skill whenever the user wants to prep for their 1-on-1 with Dorie, says things like "prep for my 1-on-1", "get ready for Dorie", "prepare for my one on one", "what should I bring to my manager meeting", "1:1 prep", or mentions preparing for a meeting with Dorie or their manager — even if they don't explicitly say "1-on-1 prep".
---

# Dorie 1-on-1 Prep

Generate a concise prep document for Matt Dupuis's recurring 1-on-1 with his manager, Dorie Blaisdell.

## Workflow

### Step 1: Ask when the last 1-on-1 was

Ask the user: "When was your last 1-on-1 with Dorie?" Accept a date in any reasonable format. Parse it to `YYYY-MM-DD`.

### Step 2: Gather source material

Read all files dated **on or after** the provided date. "Dated" means the filename starts with a date (`YYYY-MM-DD`) that is on or after the last 1-on-1 date (inclusive of that day).

Scan this directory:
- `Daily Summaries/` — AI-generated daily summaries

Read each qualifying file and extract relevant content.

### Step 3: Understand Dorie's persona

Dorie's communication style, decision-making patterns, and preferences are documented in AGENTS.md (the "Dorie Blaisdell" section). Use this understanding to shape how you frame accomplishments and topics. Key things to remember:

- She values **strategy connected to broader goals**, not just tactical updates
- She wants **clear stakeholders, dependencies, and sequencing**
- She responds well to **crisp scope** and **structured reasoning**
- She's coaching-oriented once alignment is established — bring topics where her organizational perspective adds value
- She's direct and context-first — don't bury the lead

### Step 4: Generate the prep document

Save the output to `Areas/ME/Documents/1 on 1 Prep/YYYY-MM-DD Dorie 1-on-1 Prep.md` where the date is today's date.

## Output format

Use this structure:

```markdown
---
date: YYYY-MM-DD
tags:
  - 1on1
  - dorie
  - (add 3-5 topic tags derived from the content, e.g., sso, modular-installer, pi-planning)
---

# Dorie 1-on-1 Prep — YYYY-MM-DD

## Accomplishments
- Frame each item in terms of **strategic impact**, cross-team enablement, or risk reduction — the things Dorie cares about
- Connect accomplishments to broader engineering/company goals where possible
- Be specific: name the outcome, not just the activity

## Topics for Dorie
- Things where her **organizational perspective** or **cross-department visibility** would help
- Decisions that need her input or alignment
- Areas where you need her to run interference, escalate, or connect you with someone
- Frame each as a clear question or ask, not a vague "update"

## Pressing Issues
- Blockers, risks, or time-sensitive items she should know about
- Include enough context that she can engage immediately without asking "what's the background?"

## Suggested Prep Actions
- Concrete things Matt can do before the meeting to make it more productive
- Examples: "Draft a one-pager on X so Dorie can react to it", "Get a status update from Y so you have fresh data", "Think through your recommendation on Z so you're not just raising a problem"
```

## Writing guidelines

- Favor bulleted lists over prose
- Be concise — this is a prep doc, not a report
- Each bullet should be self-contained and scannable
- For accomplishments: lead with the "so what" (the impact), then the activity
- For topics: lead with what you need from Dorie, then the context
- Don't include everything from the source material — curate ruthlessly. Only include items that directly involve Matt or his teams (Elevate, Build, Accelerate). Work owned by other teams or departments should only appear if Matt has a specific action or decision related to it.
- The filter question for every bullet: "Is this something Matt or his teams own, are blocked on, or need Dorie's help with?" If not, leave it out.
