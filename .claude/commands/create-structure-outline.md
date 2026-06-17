---
description: Create a phased implementation outline from design decisions
model: opus
---

# Structure Outline

You are tasked with creating a phased implementation outline that organizes design decisions into a clear, ordered sequence of work. This bridges the gap between "what we decided to build" and "the detailed plan for how to build it."

## Initial Response

When this command is invoked:

1. **Check if a task directory was provided**:
   - If a task directory path is provided, skip the default message
   - Immediately read all the files within the task directory FULLY
   - Use these as the foundation for the structure outline

2. **If no task directory provided**, respond with:
```
I'll help create a phased implementation outline. Please provide:
1. A task directory path (from `/create-design-discussion`) containing design artifacts
2. Or a description of what you want to structure, along with design context

I'll organize the work into incremental, testable phases.
```

Then wait for the user's input.

## Process Steps

### Step 1: Absorb Context

1. **Read all mentioned files immediately and FULLY**:
   - `*-research.md` - for codebase context and constraints
   - `*-design-discussion.md` - for design decisions and rationale
   - Any additional files mentioned
   - **IMPORTANT**: Use the Read tool WITHOUT limit/offset parameters to read entire files
   - **CRITICAL**: DO NOT spawn sub-tasks before reading these files yourself in the main context
   - **NEVER** read files partially - if a file is mentioned, read it completely

2. **Identify the building blocks**:
   - What components need to change?
   - What are the dependencies between changes?
   - What can be tested independently?
   - What needs to land together?

### Step 2: Create Initial Outline

1. **Create initial plan outline**:
   ```
   Here's my proposed plan structure:

   ## Overview
   [1-2 sentence summary]

   ## Implementation Phases:
   1. [Phase name] - [what it accomplishes]
   2. [Phase name] - [what it accomplishes]
   3. [Phase name] - [what it accomplishes]

   Does this phasing make sense? Should I adjust the order or granularity?
   ```

2. **Get feedback on structure** before writing details

3. **Key principles for phasing**:
   - Each phase should be independently testable
   - Earlier phases should unblock later ones
   - Group related changes together
   - Keep phases small enough to review but large enough to be meaningful
   - Consider migration and rollback at phase boundaries

### Step 3: Write Artifact

After structure approval:

1. **Write the structure document** to `<task-dir>/YYYY-MM-DD-structure-outline.md`:

2. **Use this template**:

```markdown
---
date: YYYY-MM-DD
task: "[task description]"
status: complete
---

# Structure Outline: [Task Description]

## Summary of Change Request
[Brief description of what we're implementing and why]

## Current State
[What exists now, what's missing, key constraints discovered]

## Desired End State
[A Specification of the desired end state after this task is complete, and how to verify it]

## What We're NOT Doing
[Explicitly list out-of-scope items to prevent scope creep]

## Current Architecture
[Describe current architecture relevant to this task. Include file:line references from research.]

## Patterns To Follow
[Conventions and patterns from the codebase that this implementation should follow.]

## Design Summary

### Q1: [Topic]
[Decision made and brief rationale — do not re-litigate]

### Q2: [Topic]
...

## Phase 1: [Descriptive Name]
**Goal**: [what this phase accomplishes]

**File Changes**:
- [component/file group]
- [component/file group]

**Validation**:
- *Automated*: [tests, commands, CI checks]
- *Manual*: [UI verification, behavior checks, or "N/A"]

## Phase 2: [Descriptive Name]
...

## Questions Resolved During Structuring
[Questions that arose during phasing and how they were resolved]
```

### Step 4: Review

1. **Present the result**:
```
I've written the structure outline to:
`<task-dir>/YYYY-MM-DD-structure-outline.md`

Task directory: `<task-dir>/`

Phases:
1. [phase summary]
2. [phase summary]
3. [phase summary]

Review the structure and let me know if you'd like to adjust anything, or proceed to `/create-plan <task-dir>`.
```

2. **Iterate based on feedback** - be ready to:
   - Add missing phases
   - Adjust technical approach
   - Clarify success criteria (both automated and manual)
   - Add/remove scope items

3. **Continue refining** until the user is satisfied

## Important Guidelines

1. **Be Interactive**:
   - Don't write the structure document in one shot
   - Get buy-in on the phasing before finalizing
   - Allow course corrections
   - Work collaboratively

2. **Be Practical**:
   - Focus on incremental, testable changes
   - Consider migration and rollback
   - Think about what can go wrong at each phase boundary
   - Include "out of scope" to prevent scope creep

3. **Read Files Fully**:
   - Always use the Read tool WITHOUT limit/offset parameters
   - If a file is mentioned, read it completely
   - The design document is your primary input - understand it thoroughly

4. **Stay Focused**:
   - This skill is about structuring, not deep research or detailed planning
   - Trust the design document's decisions - don't re-litigate them
   - Focus on ordering, granularity, dependencies, and testability
   - Leave detailed file:line references and code snippets to the plan phase

5. **No Open Questions in Final Outline**:
   - If you encounter open questions during structuring, STOP
   - Research or ask for clarification immediately
   - Do NOT finalize the structure with unresolved questions
   - Every dependency and ordering decision must be made before finalizing
