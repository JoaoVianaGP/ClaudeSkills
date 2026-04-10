---
name: task-analyze
description: Analyze a task/ticket and produce a structured Analysis.md in the TaskRegistry. Creates investigation document with root cause analysis, diagrams, hypotheses, and evidence.
argument-hint: "<task-identifier> (e.g. CORE-5375)"
---

# Task Analysis

Investigates and documents a problem or task. Produces `Analysis.md` in the TaskRegistry folder for the given task.

## Input

- `$ARGUMENTS` = task identifier (e.g. `CORE-5375`).
- If `$ARGUMENTS` is empty, **ask the user** for the task identifier before proceeding.

## Steps

1. **Create directory**: `TaskRegistry/{task-identifier}/` (if it doesn't exist).

2. **Read ticket content**: Look for `ticket.md` and any images (`.png`, `.jpg`, `.gif`, `.webp`) in the task folder. Read `ticket.md` and view all images — this is the primary source of requirements and context for the analysis. If `ticket.md` doesn't exist, ask the user to provide the ticket description or place the file there.

3. **Challenge the ticket**: Before investigating or proposing anything, critically review the ticket content:
   - Identify edge cases not covered by the ticket
   - Question technical limits (sizes, performance, timeouts)
   - Flag conflicts with existing functionality
   - Challenge implicit assumptions in the ticket
   - Document these in an **"Open Questions / Ticket Challenges"** section BEFORE any implementation proposals.

4. **Gather context**: Use the current conversation context, codebase exploration (Grep, Read, Glob), git history, and the ticket content to build understanding of the problem.

5. **Investigate**: Trace the code paths, identify the root cause or document hypotheses. Every claim must reference a specific file and line number — verify with Read/Grep before citing.

6. **Write `Analysis.md`**: Following the structure below, write the file to the TaskRegistry folder.

7. **If the file already exists**: Read it first. Update it with new findings rather than overwriting — preserve existing hypotheses and evidence, mark updated sections.

## Analysis.md Structure

```markdown
# {Title of the Problem/Task}

State: **INVESTIGATING** | **ROOT CAUSE FOUND** | **RESOLVED**

## Description

2-4 sentences: what happens, when, what's the observable impact.
Include: affected system, frequency, environment (prod/staging/dev).

## Open Questions / Ticket Challenges

- Edge cases not covered
- Technical limits questioned
- Conflicts with existing functionality
- Implicit assumptions challenged

## Root Cause

(Only if state is ROOT CAUSE FOUND or RESOLVED)

**The root cause is...** — precise description with file:line references.

### The Code

Code snippets of the critical points, with file paths and line numbers.

### Detailed Flow

Mermaid sequence diagram showing the problem path step by step.
Mark the exact point where the problem occurs.

```mermaid
sequenceDiagram
    ...
```

### Detailed Flow — Normal Path (no problem)

If applicable, show the flow that works correctly for comparison.

## Key Difference

Table or explanation of why one path fails and the other doesn't.

## Evidence

Concrete evidence (logs, timestamps, field values, test outputs):

| Time/Source | Event | Notes |
|-|-|-|
| ... | ... | ... |

## Critical Components

| Component | File | Line(s) | Relevance |
|-|-|-|-|
| ... | ... | ... | ... |

Max 8 entries. Only directly relevant components.

## Hypotheses

For each hypothesis investigated:

| # | Hypothesis | Status | Verdict |
|-|-|-|-|
| H1 | ... | CONFIRMED / DISCARDED / ACTIVE | Why |

For ACTIVE hypotheses, include what evidence would confirm or discard them.

## Proposed Fixes

(Only if root cause is found)

For each fix option:
### Fix N — Title
**File(s):** ...
**Approach:** What to change and why.
**Trade-offs:** Pros/cons of this approach.
```

## Principles

- **Evidence first**: every claim must have a source (file, line, log, test output).
- **No unfounded speculation**: if there's no evidence, say so explicitly.
- **Diagrams**: mandatory when the flow involves ≥2 components or ≥2 sequential steps. Use Mermaid `sequenceDiagram` for request/response flows, `flowchart TD` for decision paths.
- **Specific references**: always `File.cs:42`, never "in the payment service".
- **Verify before citing**: Read/Grep the actual file to confirm line numbers are current.
- **Update task-memory.md**: After writing or updating `Analysis.md`, always create or update `task-memory.md` in the same directory following the structure defined in the `/task-memory` skill.
