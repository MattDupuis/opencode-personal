---
name: git-publish
description: >-
  Interactively stages, commits, pushes, and creates or updates a GitHub PR
  for the current branch. Offers each step individually with yes/no/custom
  options, generates a contextual commit message from the diff, and respects
  the repo's existing commit style.
  USE FOR: add and commit changes, push branch, create PR, update PR, ship
  changes, publish branch, stage files, write commit message, open pull request,
  create a new branch from main/master before starting work.
  DO NOT USE FOR: rebasing, merging, resolving conflicts, managing git history.
license: MIT
compatibility: opencode
metadata:
  audience: all-engineers
  workflow: git-publish
---

# git-publish

Guide the user through creating a branch, staging, committing, pushing, and
opening or updating a GitHub pull request — one step at a time, with a
yes/no/custom prompt at each stage.

## Instructions

Work through each phase in order. At each phase, present your proposed action
and ask the user to confirm, skip, or provide custom instructions before
proceeding.

---

### Phase 0 — Branch Check

Check whether the user is on `main` or `master` before doing anything else:

1. Run `git branch --show-current`.
2. If the current branch **is `main` or `master`**, warn the user and ask:
   > **You're on `<branch>`, which is typically not where you make changes. Create a new branch?**
   > - **Yes** — suggest a branch name based on context (see below)
   > - **No** — proceed on the current branch (they know what they're doing)
   > - **Custom** — accept a specific branch name
3. If the current branch is **not** `main`/`master`, skip this phase silently.

#### Suggesting a branch name

Infer a good branch name from available context:

1. Run `git log main..HEAD --oneline` — if there are commits, use the most
   recent commit message as the basis (e.g., "fix-login-bug").
2. If no commits yet, run `git diff main --stat` — if there are unstaged
   changes, use the nature of those changes.
3. If neither exists, ask the user: "What are you working on?" and derive a
   name from their answer.

Use kebab-case, no longer than 50 characters. Prefix with a conventional type
if you can infer one (`feat/`, `fix/`, `chore/`, `docs/`, `refactor/`).

Present the suggested branch name and let the user confirm, provide a custom
name, or skip. Only create the branch when confirmed.

```bash
git checkout -b <branch-name>
```

---

### Phase 1 — Stage Files

1. Run `git status` to see what is modified, untracked, and already staged.
2. Summarize the changes to the user in plain language (no raw git output
   unless the user asks).
3. Ask:
   > **Stage files?**
   > - **Yes** — stage everything (`git add -A`)
   > - **No** — skip staging (proceed with whatever is already staged)
   > - **Custom** — ask the user which files or globs to stage

   Respect the answer exactly. If custom, run `git add` with the paths
   provided.

---

### Phase 2 — Commit

1. Run `git diff --staged` to read the full set of staged changes.
2. Read the last 5 commit messages (`git log --oneline -5`) to match the
   repo's existing style (imperative mood, length, tone).
3. Draft a commit message that:
   - Summarizes *why* the change was made, not just *what* changed
   - Uses imperative mood ("Add", "Fix", "Remove", not "Added", "Fixed")
   - Keeps the subject line under 72 characters
   - Adds a body paragraph if the change is non-trivial
4. Present the draft message and ask:
   > **Commit with this message?**
   > - **Yes** — commit as drafted
   > - **No** — skip the commit step
   > - **Custom** — accept a replacement message or additional instructions,
   >   then re-draft and confirm before committing

   Never commit until the user confirms.

---

### Phase 3 — Push

1. Check whether the current branch has an upstream (`git rev-parse
   --abbrev-ref --symbolic-full-name @{u}` — if it errors, there is no
   upstream yet).
2. Ask:
   > **Push branch `<branch-name>` to origin?**
   > - **Yes** — push (use `-u origin <branch>` if no upstream exists)
   > - **No** — skip push
   > - **Custom** — accept alternative remote or flags

---

### Phase 4 — Pull Request

1. Check whether a PR already exists for this branch:
   ```
   gh pr view --json number,title,url 2>/dev/null
   ```
2. If **no PR exists**, ask:
   > **Create a pull request?**
   > - **Yes** — draft a PR title and body (see below), then confirm before
   >   creating
   > - **No** — skip
   > - **Custom** — accept instructions for title, body, base branch, or
   >   draft status before creating

3. If a **PR already exists**, ask:
   > **Update the existing PR description?**
   > - **Yes** — re-draft the body from the current diff and confirm before
   >   updating
   > - **No** — skip
   > - **Custom** — accept instructions for what to change

#### Drafting the PR title and body

- **Title**: concise imperative sentence summarising the overall change.
- **Body**: use this structure:

  ```markdown
  ## Summary
  - <bullet: what changed and why>
  - <bullet: …>

  ## Notes
  <optional: migration steps, follow-ups, caveats>
  ```

- Base the content on the full `git diff <base-branch>...HEAD` and all
  commit messages since the branch diverged.
- Detect the base branch from the PR's existing base, or default to `main`.

#### Creating or updating the PR

- **Create**: use `gh pr create` with a HEREDOC for the body.
- **Update**: use the REST API (not `gh pr edit`) to avoid the deprecated
  Projects GraphQL API:
  ```bash
  gh api --method PATCH repos/{owner}/{repo}/pulls/{number} \
    --field body="<new body>"
  ```
  Note: `gh api` will prompt for permission — this is expected and
  intentional per org policy.

---

## Safety Rules

- Never force-push.
- Never amend a commit that has already been pushed to the remote.
- Never skip a confirmation prompt — always wait for explicit user approval
  before each git write operation.
- If the user answers "No" to a phase, skip it cleanly and move to the next.
- If the branch is `main` or `master`, warn the user before pushing and
  require explicit confirmation.
