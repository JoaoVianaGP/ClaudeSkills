---
name: task-recall
description: Re-actualize on a task by reading its TaskRegistry files. Reads task-memory.md (preferred) or falls back to Analysis.md, Implementation-review.md, and Ticket.MD. Presents condensed context for quick re-actualization.
argument-hint: "<task-identifier> (e.g. CORE-5375)"
---

# Task Recall

Re-actualizes context on a task from the TaskRegistry at `C:\Users\JoãoViana-GP\Desktop\TaskRegistry\{task-identifier}/`.

## Input

- `$ARGUMENTS` = task identifier (e.g. `CORE-5375`).
- If `$ARGUMENTS` is empty, **ask the user** for the task identifier before proceeding.

## Steps

1. **Locate task folder**: `C:\Users\JoãoViana-GP\Desktop\TaskRegistry\{task-identifier}/`.
   - If the folder does not exist, tell the user and ask if they want to create it with `/task-analyze`.

2. **List all files** in the task folder to understand what's available.

3. **Read `task-memory.md`** if it exists — this is the primary and preferred source. It's designed to be the single entry point for re-actualization.

4. **If `task-memory.md` does not exist**, read these files in order (all optional):
   - `Ticket.MD` / `ticket.md` — original task description
   - `ParentTicket.MD` — parent ticket context (if exists)
   - `Analysis.md` — problem investigation and root cause
   - `Implementation-review.md` — implementation details and decisions
   - Any other `.md` files in the folder (reference docs)

5. **Check current branch and git state**:
   - Run `git branch --show-current` to see if we're on the task's branch.
   - If the current branch matches the task identifier, run `git log --oneline master..HEAD` to show recent commits on this branch.
   - Run `git status` to show working tree state.

6. **Present a re-actualization summary** to the user with:
   - **Task**: identifier and short title
   - **State**: current state (from task-memory or inferred)
   - **Problem**: what the task is about (2-3 sentences)
   - **Where we left off**: last known progress, current state, what was done
   - **Key decisions**: critical decisions made so far (especially rejected approaches)
   - **Next steps**: what remains to be done
   - **Git state**: current branch, uncommitted changes, recent commits
   - **Source**: which files were read (so user knows what's available)

7. **If `task-memory.md` did not exist** and there was enough information to create one, suggest running `/task-memory {task-identifier}` to create it for future re-actualizations.

## Output Format

Present the summary directly — no preamble. Use this structure:

```
## {TASK-ID} — {Short Title}

**State**: {state}  |  **Branch**: {branch}  |  **Last Updated**: {date}

### Problem
{2-3 sentences}

### Where We Left Off
{Current progress, last actions taken}

### Key Decisions
| Decision | Outcome | Why |
|-|-|-|

### Next Steps
{Numbered list of what remains}

### Git State
- Branch: {branch name}
- Commits ahead of master: {count}
- Working tree: {clean / N files modified}

---
*Sources read: {list of files}*
```

## Principles

- **Speed over depth**: the goal is fast re-actualization (under 2 minutes of reading), not comprehensive analysis.
- **Don't duplicate content**: summarize, don't echo back entire file contents.
- **Prefer task-memory.md**: if it exists and is up-to-date, it should be sufficient. Only read other files if task-memory.md is missing or clearly outdated.
- **Show git context**: the user needs to know where they are in the codebase, not just in the documentation.
- **Actionable output**: end with clear next steps so the user can immediately continue working.
