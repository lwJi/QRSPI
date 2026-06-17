---
description: Write a detailed implementation plan from prior QRSPI artifacts
model: opus
---

# Create Plan

You are tasked with writing a detailed, actionable implementation plan. This is the final planning step in the QRSPI pipeline - all research, design decisions, and structural outline have already been completed. Your job is to fill in the details.

## Initial Response

When this command is invoked:

1. **Check if a task directory was provided**:
   - If a task directory path is provided, skip the default message
   - Immediately read all the files within the task directory FULLY

2. **If no task directory provided**, respond with:
```
I'll help write a detailed implementation plan. Please provide:
1. A task directory path (from `/create-structure-outline`) containing prior artifacts
2. Or at minimum a structure outline and design decisions

I'll fill in the implementation details for each phase.

Tip: Run the full QRSPI pipeline for best results:
/create-research-questions → /create-research → /create-design-discussion → /create-structure-outline → /create-plan
```

Then wait for the user's input.

## Process Steps

### Step 1: Absorb Context

1. **Read all mentioned files immediately and FULLY**:
   - `*-research.md` - for codebase context and constraints
   - `*-design-discussion.md` - for design decisions and rationale
   - `*-structure-outline.md` - for phase structure and scope
   - Any additional files mentioned
   - **IMPORTANT**: Use the Read tool WITHOUT limit/offset parameters to read entire files
   - **CRITICAL**: DO NOT spawn sub-tasks before reading these files yourself in the main context
   - **NEVER** read files partially - if a file is mentioned, read it completely

2. **Read all source files referenced** in the research and design artifacts:
   - Key files mentioned with `file:line` references
   - Configuration files relevant to the changes
   - Test files that reveal expected behavior

3. **Build a mental model**:
   - What is the current state?
   - What are we building toward?
   - What design decisions constrain us?
   - What is the phase structure?

### Step 2: Write Detailed Plan

1. **Write the plan** to `<task-dir>/YYYY-MM-DD-plan.md`

2. **Use this template structure**:

````markdown
---
date: YYYY-MM-DD
task: "<task description>"
status: complete
---

# [Feature/Task Name] Implementation Plan

## Overview

[Brief description of what we're implementing and why]

## Current State Analysis

[What exists now, what's missing, key constraints discovered]

## Desired End State

[A Specification of the desired end state after this plan is complete, and how to verify it]

### Key Discoveries:
- [Important finding with file:line reference]
- [Pattern to follow]
- [Constraint to work within]

## What We're NOT Doing

[Explicitly list out-of-scope items to prevent scope creep]

## Implementation Approach

[High-level strategy and reasoning]

## Phase 1: [Descriptive Name]

### Overview
[What this phase accomplishes]

### Changes Required:

#### 1. [Component/File Group]
**File**: `path/to/file.ext`
**Changes**: [Summary of changes]

```[language]
// Specific code to add/modify
```

### Success Criteria:

#### Automated Verification:
- [ ] Migration applies cleanly: `make migrate`
- [ ] Unit tests pass: `make test-component`
- [ ] Type checking passes: `npm run typecheck`
- [ ] Linting passes: `make lint`
- [ ] Integration tests pass: `make test-integration`

#### Manual Verification:
- [ ] Feature works as expected when tested via UI
- [ ] Performance is acceptable under load
- [ ] Edge case handling verified manually
- [ ] No regressions in related features

**Implementation Note**: After completing this phase and all automated verification passes, pause here for manual confirmation from the human that the manual testing was successful before proceeding to the next phase.

---

## Phase 2: [Descriptive Name]

[Similar structure with both automated and manual success criteria...]

---

## Testing Strategy

### Unit Tests:
- [What to test]
- [Key edge cases]

### Integration Tests:
- [End-to-end scenarios]

### Manual Testing Steps:
1. [Specific step to verify feature]
2. [Another verification step]
3. [Edge case to test manually]

## Performance Considerations

[Any performance implications or optimizations needed]

## Migration Notes

[If applicable, how to handle existing data/systems]

## References

- Original ticket: `<task-dir>/ticket.md`
- Related research: `<task-dir>/YYYY-MM-DD-research.md`
- Design decisions: `<task-dir>/YYYY-MM-DD-design-discussion.md`
- Structure outline: `<task-dir>/YYYY-MM-DD-structure-outline.md`
- Similar implementation: `[file:line]`
````

3. **Key principles for filling in details**:
   - Pull phase structure directly from `structure-outline.md` - don't restructure
   - Pull design decisions from `design-discussion.md` - don't revisit
   - Pull code references from `research.md` - include specific `file:line` refs
   - Add concrete file paths, code snippets, and specific changes
   - Write measurable success criteria for each phase

### Step 3: Review

1. **Present the draft plan location**:
   ```
   I've created the implementation plan at:
   `<task-dir>/YYYY-MM-DD-plan.md`

   Task directory: `<task-dir>/`

   Please review it and let me know:
   - Are the phases properly scoped?
   - Are the success criteria specific enough?
   - Any technical details that need adjustment?
   - Missing edge cases or considerations?
   ```

2. **Iterate based on feedback** - be ready to:
   - Add missing phases
   - Adjust technical approach
   - Clarify success criteria (both automated and manual)
   - Add/remove scope items

3. **Continue refining** until the user is satisfied

## Important Guidelines

1. **Be Thorough**:
   - Read all context files COMPLETELY before writing the plan
   - Include specific file paths and line numbers
   - Write measurable success criteria with clear automated vs manual distinction
   - Automated steps should use `make` whenever possible - for example `make -C humanlayer-wui check` instead of `cd humanlayer-wui && bun run fmt`

2. **Be Practical**:
   - Focus on incremental, testable changes
   - Consider migration and rollback
   - Think about edge cases
   - Include "what we're NOT doing"

3. **Track Progress**:
   - Use TodoWrite to track planning tasks
   - Update todos as you complete research
   - Mark planning tasks complete when done

4. **No Open Questions in Final Plan**:
   - If you encounter open questions while writing, STOP
   - Research or ask for clarification immediately
   - Do NOT write the plan with unresolved questions
   - The implementation plan must be complete and actionable
   - Every decision must be made before finalizing the plan

## Success Criteria Guidelines

**Always separate success criteria into two categories:**

1. **Automated Verification** (can be run by execution agents):
   - Commands that can be run: `make test`, `npm run lint`, etc.
   - Specific files that should exist
   - Code compilation/type checking
   - Automated test suites

2. **Manual Verification** (requires human testing):
   - UI/UX functionality
   - Performance under real conditions
   - Edge cases that are hard to automate
   - User acceptance criteria

**Format example:**
```markdown
### Success Criteria:

#### Automated Verification:
- [ ] Database migration runs successfully: `make migrate`
- [ ] All unit tests pass: `go test ./...`
- [ ] No linting errors: `golangci-lint run`
- [ ] API endpoint returns 200: `curl localhost:8080/api/new-endpoint`

#### Manual Verification:
- [ ] New feature appears correctly in the UI
- [ ] Performance is acceptable with 1000+ items
- [ ] Error messages are user-friendly
- [ ] Feature works correctly on mobile devices
```
