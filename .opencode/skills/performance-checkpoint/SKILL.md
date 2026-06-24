---
name: performance-checkpoint
description: Augment a Performance Checkpoint Document with context from its meeting transcript. Use this skill whenever the user wants to enrich, augment, or add context to a performance checkpoint document, says things like "augment my checkpoint", "add transcript context to my checkpoint", "enrich my performance doc", "process my checkpoint transcript", or pastes a transcript alongside a checkpoint document reference -- even if they don't explicitly say "augment" or "performance checkpoint". Also use when the user mentions a checkpoint document and a transcript together, or asks to combine checkpoint notes with meeting context.
---

# Performance Checkpoint Augmenter

## What This Skill Does

Takes two inputs -- a Performance Checkpoint Document and its corresponding meeting transcript -- and weaves transcript context into the document as inline annotations. The original document text is preserved exactly; context from the transcript is added beneath relevant items to provide richer detail, direct quotes, and nuance that the document alone doesn't capture.

A summary section is appended at the end with actionable recommendations.

## Inputs

1. **Performance Checkpoint Document**: Found in `Areas/ME/Performance Checkpoint/` with naming format `YYYY-MM-DD Performance Checkpoint Document.md`
2. **Meeting Transcript**: Either:
   - A file in the same folder (e.g., `YYYY-MM-DD Performance Checkpoint Transcript.md`)
   - Pasted directly into chat by the user
   - Raw Zoom transcript (speaker/timestamp format) or pre-processed summary format

If the user provides only one input, ask for the other. If only a date is given, look for both files matching that date in `Areas/ME/Performance Checkpoint/`.

## Core Rule: Preserve Original Text

The existing text in the Performance Checkpoint Document must not be changed. Not a word, not punctuation, not formatting. The document represents what the user wrote going into the meeting -- that's the anchor. Everything added is supplementary.

## How to Add Context

For each section and item in the document, scan the transcript for relevant discussion. When you find meaningful additions, insert an Obsidian callout block directly below the relevant bullet or paragraph. Use the `transcript` callout type with a collapsed (`-`) modifier so the context is visually distinct but doesn't overwhelm the original text:

```markdown
- Original bullet point text here
  > [!transcript]- Transcript Context
  > Additional detail or nuance from the discussion that enriches this point.
  > 
  > Another relevant detail from the transcript.
  > 
  > *Speaker:* "Direct quote when exact wording matters"
```

The callout renders with a distinct colored bar in Obsidian, making it immediately clear what's original vs. added. The `-` modifier makes it collapsed by default so the document stays scannable. Users can expand individual callouts to read the context.

Group all transcript context for a given bullet into a single callout rather than creating multiple callouts per bullet. If a section-level heading has context that doesn't belong to any specific bullet, place the callout directly under the heading.

### What counts as meaningful context

Add context when the transcript provides:
- **Specific details** not in the document (names, dates, numbers, examples)
- **Decisions made** during the discussion of that item
- **Feedback from the other participant** (Dorie's reactions, coaching, pushback)
- **Nuance or color** that changes how the item should be understood
- **Follow-up commitments** that emerged from discussing the item
- **Direct quotes** where exact wording carries weight (praise, concerns, commitments, guidance)

Skip adding context when:
- The transcript just restates what's already in the document
- The discussion was brief acknowledgment ("yep", "sounds good") without substance
- The context would be noise rather than signal

### Formatting conventions
- Use `[[ME]]` for Matt Dupuis. Never `[[Matt Dupuis]]` or `[[ME|Matt Dupuis]]`.
- Use `[[Person Name]]` wiki-links for other people mentioned.
- Indent context callouts one level deeper than the item they annotate.
- Use `*Speaker:* "quote"` for verbatim quotes inside callouts.

## Summary Section

After all the augmented sections, add a `## Summary & Recommendations` section at the end. This section has two parts:

### Checkpoint Reflection
A brief (2-3 paragraph) synthesis of the checkpoint as a whole: what themes emerged, what Dorie's overall sentiment was, and how the checkpoint positions Matt relative to his goals.

### Actionable Recommendations
For each action item in the document, provide concrete next steps the user can take. Also include recommendations that emerge from the transcript discussion even if they weren't captured as formal action items. Format:

```markdown
### Action: [action item text]
**Why it matters:** Brief context on why this was raised and what impact it has
**Recommended steps:**
1. Specific step with timeline
2. Another specific step
3. ...
**Success looks like:** What a good outcome would be
```

Also include a final subsection:

### Making the Next Checkpoint More Impactful
Practical advice based on patterns in this checkpoint -- things like: areas where Matt could have come with more concrete data, topics Dorie seemed most engaged by (lean into those), opportunities to demonstrate strategic thinking, or ways to better frame wins.

## Output

Write the augmented document back to the same file path, overwriting the original. The user's vault is version-controlled, so this is safe -- they can always revert.

## Example

Given a document bullet:
```markdown
### Okta Slack Channel (Done)
- Sales rep suggested a Slack channel - we now have this with Woody
- When Woody or Okta reach out, respond. That's part of care and feeding the relationship with external vendors
```

And transcript context about Woody's baseball invite and relationship maintenance, the augmented version would be:

```markdown
### Okta Slack Channel (Done)
- Sales rep suggested a Slack channel - we now have this with Woody
- When Woody or Okta reach out, respond. That's part of care and feeding the relationship with external vendors
  > [!transcript]- Transcript Context
  > Dorie emphasized this extends to social invitations too -- even declining a baseball invite is relationship maintenance.
  > 
  > *Dorie:* "Even if you can't go, just responding shows you value the relationship."
  > 
  > Action: Respond to Woody's pending baseball invite.
```
