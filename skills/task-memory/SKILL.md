---
name: task-memory
description: Create or update the task-memory.md file for a task in the TaskRegistry. Synthesizes Analysis.md and Implementation-review.md into a condensed memory file for quick re-actualization.
argument-hint: "<task-identifier> (e.g. CORE-5375)"
---

# Task Memory

Creates or updates `task-memory.md` in the TaskRegistry folder for the given task.
This file is the **single entry point** for re-actualizing on a task in future conversations.

## Input

- `$ARGUMENTS` = task identifier (e.g. `CORE-5375`).
- If `$ARGUMENTS` is empty, **ask the user** for the task identifier before proceeding.

## Steps

1. **Locate task folder**: `TaskRegistry/{task-identifier}/`.

2. **Read existing sources** (in this order, all optional):
   - `task-memory.md` — if it exists, read it first to understand current state and preserve manually added content.
   - `Analysis.md` — problem investigation, root cause, hypotheses, evidence.
   - `Implementation-review.md` — implementation iterations, code changes, decisions.
   - Current conversation context — any development work or analysis just completed.

3. **Synthesize into `task-memory.md`** following the structure below. The goal is a **concise, self-contained** file that gives full context in under 3 minutes of reading.

4. **If `task-memory.md` already exists**: Read it, then update it — don't overwrite manually added sections or notes. Merge new information, update the state and timestamp.

## task-memory.md Structure

```markdown
# {TASK-ID} — {Short Title}

**State**: INVESTIGATING | IN PROGRESS | IN REVIEW | DONE
**Last Updated**: {YYYY-MM-DD}

## Problem

2-3 sentences: what happens, when, observable impact, environment.

## Root Cause

(Only if found) Concise explanation of the root cause with key file:line references.
Max 1 paragraph — the full analysis is in Analysis.md.

## Solution

(Only if implemented) Approach adopted, summarized in 1-2 paragraphs.
Focus on the "why" of the approach, not the "what" of each file change.

## Key Files

Files that are critical to understand or that were modified. Max 10 entries.

| File | Role |
|-|-|
| `path/to/file.cs` | What this file does in the context of this task |

## Key Decisions

Decisions that shaped the implementation, especially rejected approaches and why.

| Decision | Outcome | Why |
|-|-|-|
| V1: Hardcoded skipDarkProcessing | Rejected | Breaks middleware auto-sign (Peter's feedback) |

## Open Questions

Numbered list of unresolved questions or deferred decisions.

## Related Docs

- [Analysis](Analysis.md) — root cause investigation, detailed flows, evidence
- [Implementation Review](Implementation-review.md) — code changes, before/after, layer-by-layer
```

## Principles

- **Conciseness over completeness**: task-memory.md is a summary, not a duplicate. Point to Analysis.md and Implementation-review.md for details.
- **Self-contained context**: a reader of task-memory.md alone should understand what the task is about, what was decided, and what's pending — without reading the other files.
- **Key Decisions are critical**: the most valuable part is _why_ certain approaches were chosen or rejected. Future conversations need this to avoid re-exploring dead ends.
- **State must be current**: always update the State and Last Updated fields.
- **No code snippets**: keep it prose and tables. Code details belong in the other documents.
