---
name: github-issues-dashboard
description: Show all GitHub issues and PRs assigned to the user or where they're mentioned, grouped by repository. Use this skill whenever the user asks to see their GitHub issues, says "what issues are assigned to me", "show my GitHub tasks", "what am I mentioned in", "GitHub dashboard", "review my open issues", "what's on my GitHub plate", "my workload", "show my PRs", or anything about their GitHub tracking — even if they don't explicitly say "issues" or "assigned." This is the right skill for any query about the user's GitHub items across all repos.
---

# GitHub Issues Dashboard

Shows a consolidated view of all GitHub issues and pull requests assigned to the user or where they're @mentioned, organized by repository. This works across all repos the user has access to — no need to specify individual repos.

## Workflow

### 1. Get the GitHub username

Retrieve the logged-in username using the GitHub API:

```bash
gh api user --jq '.login'
```

If `gh` is not authenticated, tell the user and guide them to run `gh auth login --scopes "repo,read:org"`.

### 2. Determine the state filter

Decide what to show and store the result as `{state_qualifier}`:

- **Default**: open items only → `{state_qualifier}` = `"state:open"`
- **Toggle**: If the user asks for closed, all, or any other state, use that as the search qualifier instead.
  - closed → `{state_qualifier}` = `"state:closed"`
  - all → `{state_qualifier}` = *(empty — omit the state qualifier argument entirely)*
  - Any other state → set accordingly

Every later step that constructs a search query uses `{state_qualifier}` in place of a hardcoded state filter.

### 3. Run cross-repo searches

Run these two queries in parallel using `gh search issues`. Always include `--include-prs` so pull requests appear alongside issues.

```bash
# Issues/PRs assigned to the user
gh search issues {state_qualifier} --assignee "@me" --include-prs --limit 50 \
  --json title,url,repository,state,number,updatedAt,author,isPullRequest

# Issues/PRs mentioning the user
gh search issues {state_qualifier} --mentions {username} --include-prs --limit 50 \
  --json title,url,repository,state,number,updatedAt,author,isPullRequest
```

- Default limit is 50 per query. Accept `--limit N` or "show me N" to override.
- If the queries return errors (e.g., rate limiting), inform the user and suggest retrying.

### 4. Merge, deduplicate, and determine role

Combine both result sets. For each item, determine the user's role using this logic in priority order:

1. **Assigned** — Item came from the `--assignee "@me"` query. The user is a direct assignee.
2. **Author** — Item was created by the user (check `author` field matches the username).
3. **Mentioned** — Item came from the `--mentions` query but was not in the assigned results.

If an item appears in both assigned + mentioned results, keep only one entry with role `[assigned]`. Deduplicate by URL.

A single item may match multiple roles (e.g., user is both assigned and author). Show only the highest-priority role: assigned > author > mentioned.

### 5. Format the dashboard

Every item must show four things at a glance:
1. **Your role** — are you assigned, mentioned, or the author?
2. **Item type** — is it an issue or a pull request?
3. **Where it lives** — which repository (or project) it belongs to
4. **A clickable link** — make the issue/PR number (`#{number}`) a markdown link to its URL so the user can easily copy or click it

Group results by repository (`nameWithOwner`). Use `📦` as a visual prefix on repo headings to signal these are repositories (not projects). Within each repo, sort: open items first, then closed/merged. Within same state, sort by `updatedAt` descending (most recently updated first).

Use this format:

```markdown
# GitHub Issues Dashboard ({state filter})

## 📦 {org}/{repo}
- [{role}] [{type}]{state_icon} [#{number}]({url}) {title} ({date})
```

**Role badges** (always shown, every item):
- `[assigned]` — user is assigned to this item
- `[author]` — user created this item
- `[mentioned]` — user is @mentioned but not assigned

**Type badges** (always shown, every item, derived from `isPullRequest` field):
- `[issue]` — a GitHub issue
- `[pr]` — a pull request

**State nuance** (shown when state is not simply open):
- An open issue or PR with `[pr]` type badge already tells you it's a PR — state icons are optional but can help. Only add state icons when the state deviates from "open":
- Closed issue or closed unreviewed PR → add `❌` after the badges
- Merged PR → add `✅` after the badges

**Date format:** Show the date portion of `updatedAt` (e.g., `2026-06-17`). Use the actual date string, not relative time.

### 6. Add a summary footer

After the grouped list:

```markdown
---
{total} {state_label} items across {repo_count} repositories
Say "show closed" or "show all" to change the filter.
```

The `{state_label}` should match the current filter: `"open"`, `"closed"`, or `""` (omit the word — just "items"). For mixed views like "open and closed", use "open/closed".

### 7. Save to Obsidian inbox and display

First, determine the Obsidian inbox path. The vault root is at:
```
/mnt/c/Users/m.dupuis/OneDrive - TechSmith Corporation/Documents/Obsidian/Bag Of Holding
```
The Inbox/ subdirectory is where new notes land:
```
/mnt/c/Users/m.dupuis/OneDrive - TechSmith Corporation/Documents/Obsidian/Bag Of Holding/Inbox/
```

Create the inbox directory if it doesn't exist, then write the full formatted dashboard to a dated markdown file:

```bash
mkdir -p "/mnt/c/Users/m.dupuis/OneDrive - TechSmith Corporation/Documents/Obsidian/Bag Of Holding/Inbox"
cat > "/mnt/c/Users/m.dupuis/OneDrive - TechSmith Corporation/Documents/Obsidian/Bag Of Holding/Inbox/$(date +%Y-%m-%d) GitHub Issues Dashboard.md" << 'DASHBOARD_EOF'
{dashboard content}
DASHBOARD_EOF
```

Replace `{dashboard content}` with the full formatted markdown from sections 5-6. The `date` command gives a clean YYYY-MM-DD prefix so files sort naturally.

After writing the file, also print the dashboard directly in the conversation so the user sees it immediately.

## Edge Cases

- **No results**: If either query returns empty, just use whatever results the other query produced. If both are empty: "No open issues or PRs found across your repos."
- **Not authenticated**: `gh auth status` fails — tell the user to run `gh auth login --scopes "repo,read:org"`
- **Rate limiting**: If a query returns an HTTP 403 or rate limit error, tell the user and suggest waiting a few minutes
- **Empty repos section**: If a repo has only closed items and the filter is `state:open`, those items won't appear — this is correct behavior
- **No repos match**: If after dedup there are zero items, say so plainly — don't show empty section headers
