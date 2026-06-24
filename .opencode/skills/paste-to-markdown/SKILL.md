---
name: paste-to-markdown
description: Convert pasted text from any source into a clean, well-structured markdown file and save it to the Obsidian inbox. Use this skill whenever the user pastes a block of text (article, email, webpage content, notes, documentation, etc.) and wants it saved, clipped, or filed - even if they just say "save this", "clip this", "put this in my inbox", "file this away", "clean this up and save it", or paste content and say something like "markdown this" or "inbox". Also use when the user says "clip" or "inbox" alongside pasted content, or asks to convert copied text into a note.
---

# Inbox Clipper

You take pasted content from any source - web articles, emails, Slack messages, documentation, PDFs, meeting notes, whatever - and transform it into a clean, well-structured markdown file saved to the Obsidian inbox.

## Inbox location

Save all files to:
```
/mnt/c/users/m.dupuis/onedrive - TechSmith Corporation/documents/obsidian/bag of holding/inbox/
```

## Workflow

1. **Receive the pasted content** from the user.

2. **Propose a filename** based on the content. Pick something short, descriptive, and kebab-cased (e.g., `react-server-components-overview.md`). Tell the user what you're going to name it and give them a chance to override - but don't block on it. Frame it as: "I'll save this as `proposed-name.md` - let me know if you'd prefer a different name."

3. **Transform the content** into clean, well-structured markdown:
   - Strip all HTML tags, tracking pixels, and formatting artifacts
   - Establish a clear heading hierarchy (single H1 as the title, logical H2/H3 structure beneath)
   - Clean up lists - normalize bullet styles, fix indentation, break run-on lists into proper items
   - Preserve meaningful links (strip tracking parameters from URLs)
   - Remove redundant whitespace, fix paragraph breaks
   - If the content is disorganized, reorganize it into logical sections
   - If there are obvious sections that could benefit from headers, add them
   - Preserve code blocks with appropriate language tags
   - Convert tables to markdown tables where appropriate
   - Add a brief YAML frontmatter block with: `title`, `source` (if identifiable), `date_clipped` (today's date)
   - **Never use emdashes (—)** in the output. Use regular dashes, colons, or restructure the sentence instead.
   - **Use tight line spacing** - no blank lines between list items, no blank lines between a heading and the content that immediately follows it, and only one blank line between major sections. The output should feel compact and scannable, not airy.

4. **Write the file** to the inbox folder.

## What "heavy restructuring" means

Don't just naively convert formatting. Actually read and understand the content, then present it in the clearest possible structure. This means:

- If an article buries the lede, you can reorder for clarity
- If there are repetitive sections, consolidate them
- If the original has no headings but covers distinct topics, add section headers
- If there's a wall of text that's really a list, make it a list
- Preserve the original meaning and detail - don't summarize away content unless it's truly noise (ads, navigation elements, "subscribe to our newsletter", etc.)

The goal is: if someone opens this note in Obsidian six months from now, they should be able to quickly scan the structure and find what they need.

## What NOT to do

- Don't add commentary or analysis beyond the source material
- Don't summarize unless the user asks for a summary
- Don't add tags or links to other notes (the user will do that during their inbox processing)
- Don't ask a bunch of clarifying questions - just do it. The filename proposal is the one place you pause, and even that's non-blocking.
