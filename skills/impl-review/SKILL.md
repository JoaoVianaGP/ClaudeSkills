---
name: impl-review
description: Create or update the implementation review document in the TaskRegistry. Documents what changed, why, step-by-step code flow, and comparison of approaches if there were iterations.
argument-hint: "<task-identifier> (e.g. CORE-5375)"
---

# Implementation Review

Creates or updates `Implementation-review.md` in the TaskRegistry folder for the given task.

## Input

- `$ARGUMENTS` = task identifier (e.g. `CORE-5375`).
- If `$ARGUMENTS` is empty, **ask the user** for the task identifier before proceeding.

## Steps

1. **Create directory**: `TaskRegistry/{task-identifier}/` (if it doesn't exist).

2. **Gather implementation data**:
   - Run `git log` to understand the commits on the current branch vs master.
   - Run `git diff master...HEAD` to see all changes.
   - Read the conversation context for rejected approaches, PR feedback, design decisions.
   - If an `Analysis.md` exists in the TaskRegistry folder, read it for problem context.

3. **Analyze changes by layer**: Group file changes by architectural layer (GQL input, payload, service, domain, adapter, hook, frontend, etc.).

4. **Write or update `Implementation-review.md`**:
   - If the file **already exists**: Read it first. Update with the latest implementation state (add new iteration sections, update the current implementation details, refresh the step-by-step flow). Preserve previous iteration history — don't overwrite rejected approaches.
   - If the file **does not exist**: Create it from scratch following the structure below.

## Implementation-review.md Structure

```markdown
# {Task-ID} — {Short Title}

## Problem

Concise summary (3-5 sentences) of the problem that motivated this implementation.
Link to the root cause if an Analysis.md exists.

---

## Implementation V{N} — {Name} ({Status: Rejected/Current/Approved})

(Repeat this section for each implementation iteration. If there was only one, just have one section.)

### Approach

1-2 paragraphs explaining the approach and its rationale.

### Files Changed ({count})

| File | Change |
|-|-|
| `path/to/file.cs` | What was changed |

### How It Worked

Text diagram or description showing the flow.

### Why It Was {Accepted/Rejected}

Detailed analysis. If rejected, include who rejected it, their reasoning, and why it makes sense.

### Analysis

| Aspect | Assessment |
|-|-|
| Fixes the problem? | Yes/No — explanation |
| Backward compatible? | Yes/No — explanation |
| ... | ... |

---

## {Final Implementation} — Files Changed by Layer

For the accepted/current implementation, detail every file change with the reason:

### Layer N: {Layer Name}

| File | Change | Why |
|-|-|-|
| `path/to/file.cs` | What was changed | Why this change was necessary |

---

## Step-by-Step Flow with Code Changes

This is the key section. For each step in the execution flow, show:
1. What happens at this step
2. Which file was changed
3. The before/after code — **IMPORTANT**: "BEFORE" always means the original code on the base branch (e.g. `master`), not a previous iteration within the same feature branch. Use `git diff master...HEAD` as the source of truth. The goal is to show what changed relative to the state before any development started.
4. What passes to the next step

Format each step as:

```
STEP N: {Description}
```

**File**: `path/to/file.ext`
**Change**: {What was changed}
```language
// BEFORE:
...

// AFTER:
...
```

```
  ↓ {what flows to the next step}
```

---

(Repeat for all steps)

At the end, include the alternative flow (e.g., middleware path) showing how the default behavior is preserved.

## V1 vs V2 — Direct Comparison

(Only if there were multiple iterations)

| Criterion | V1 | V2 |
|-|-|-|
| ... | ... | ... |

## Impact Analysis — Other Callers / Components

Table of other components that interact with the changed code and whether they were affected:

| Caller | File | Behavior | Notes |
|-|-|-|-|
| ... | ... | ... | Why affected or not |

## Verification

How to test the changes:
1. Build verification
2. Existing tests
3. Manual testing scenarios (for each relevant flow)
4. Regression checks

## Open Questions

Numbered list of unresolved questions or decisions deferred for later.
```

## Principles

- **Trace the full chain**: Don't just list files changed — show how data flows through them step by step.
- **Before/After code**: "BEFORE" is always the original code on the base branch (e.g. `master`), not a previous iteration on the feature branch. Use `git diff master...HEAD` as source of truth.
- **Why, not just what**: Every file change must explain WHY it was necessary, not just what changed.
- **Verify line numbers**: Read/Grep actual files to confirm code snippets are current.
- **Include rejected approaches**: If there were iterations, document what was tried and why it was rejected — this is valuable context for reviewers and future maintainers.
- **Impact analysis**: Always check who else calls the changed functions/methods and document whether they're affected.
- **Update task-memory.md**: After writing or updating `Implementation-review.md`, always create or update `task-memory.md` in the same directory following the structure defined in the `/task-memory` skill.
