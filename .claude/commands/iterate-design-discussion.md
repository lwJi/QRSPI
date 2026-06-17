---
description: Iterate on existing design discussions with thorough research and updates
model: opus
---

# Iterate Design Discussion

You are tasked with updating existing design discussions based on user feedback. You should be skeptical, thorough, and ensure changes are grounded in actual codebase reality.

## Initial Response

When this command is invoked:

1. **Parse the input to identify**:
   - Design discussion file path (e.g., `thoughts/shared/tasks/<description>/YYYY-MM-DD-design-discussion.md`)
   - Requested changes/feedback

2. **Handle different input scenarios**:

   **If NO design discussion file provided**:
   ```
   I'll help you iterate on an existing design discussion.

   Which design discussion would you like to update? Please provide the path to the file (e.g., `thoughts/shared/tasks/<description>/YYYY-MM-DD-design-discussion.md`).

   Tip: You can list recent design discussions with `find thoughts/shared/tasks -name "*design-discussion.md" -type f | head`
   ```
   Wait for user input, then re-check for feedback.

   **If design discussion file provided but NO feedback**:
   ```
   I've found the design discussion at [path]. What changes would you like to make?

   For example:
   - "Revisit the decision on Q2 — I think Option B is better after all"
   - "Add a new design question about caching strategy"
   - "Update the current architecture section with the new service"
   - "Narrow the scope — remove the migration piece"
   - "Add patterns we should follow from the new auth module"
   ```
   Wait for user input.

   **If BOTH design discussion file AND feedback provided**:
   - Proceed immediately to Step 1
   - No preliminary questions needed

## Process Steps

### Step 1: Read and Understand Current Design Discussion

1. **Read the existing design discussion file COMPLETELY**:
   - Use the Read tool WITHOUT limit/offset parameters
   - Understand the current structure: summary, current state, desired end state, architecture, design questions, and decisions
   - Note the patterns to follow and implementation notes
   - Identify which design questions have been decided vs. open

2. **Understand the requested changes**:
   - Parse what the user wants to add/modify/remove
   - Identify if changes require codebase research
   - Determine scope of the update

### Step 2: Research If Needed

**Only spawn research tasks if the changes require new technical understanding.**

If the user's feedback requires understanding new code patterns or validating assumptions:

1. **Create a research todo list** using TodoWrite

2. **Spawn parallel sub-tasks for research**:
   Use the right agent for each type of research:

   **For code investigation:**
   - **codebase-locator** - To find relevant files
   - **codebase-analyzer** - To understand implementation details
   - **codebase-pattern-finder** - To find similar patterns

   **Be EXTREMELY specific about directories**:
   - Include full path context in prompts

3. **Read any new files identified by research**:
   - Read them FULLY into the main context
   - Cross-reference with the design discussion requirements

4. **Wait for ALL sub-tasks to complete** before proceeding

### Step 3: Present Understanding and Approach

Before making changes, confirm your understanding:

```
Based on your feedback, I understand you want to:
- [Change 1 with specific detail]
- [Change 2 with specific detail]

My research found:
- [Relevant code pattern or constraint]
- [Important discovery that affects the change]

I plan to update the design discussion by:
1. [Specific modification to make]
2. [Another modification]

Does this align with your intent?
```

Get user confirmation before proceeding.

### Step 4: Update the Design Discussion

1. **Make focused, precise edits** to the existing design discussion:
   - Use the Edit tool for surgical changes
   - Maintain the existing structure unless explicitly changing it
   - Keep all file:line references accurate
   - Update design question decisions if revisited

2. **Ensure consistency across sections**:
   - If revisiting a design question, update the recommendation and rationale
   - If changing scope, update "What We're NOT Doing" section
   - If modifying architecture understanding, update "Current Architecture" section
   - If discovering new patterns, update "Patterns To Follow" section
   - If changing approach, update "High-Level Approach" section

3. **Preserve quality standards**:
   - Include specific file paths and line numbers for new content
   - Use `make` commands for automated verification where applicable
   - Keep language clear and actionable
   - Ensure design questions have clear recommendations with rationale

### Step 5: Review

**Present the changes made**:
   ```
   I've updated the design discussion at `<task-dir>/YYYY-MM-DD-design-discussion.md`

   Changes made:
   - [Specific change 1]
   - [Specific change 2]

   The updated design discussion now:
   - [Key improvement]
   - [Another improvement]

   Would you like any further adjustments, or proceed to `/create-structure-outline <task-dir>`?
   ```

**Be ready to iterate further** based on feedback

## Important Guidelines

1. **Be Skeptical**:
   - Don't blindly accept change requests that seem problematic
   - Question vague feedback - ask for clarification
   - Verify technical feasibility with code research
   - Point out potential conflicts with existing design decisions

2. **Be Surgical**:
   - Make precise edits, not wholesale rewrites
   - Preserve good content that doesn't need changing
   - Only research what's necessary for the specific changes
   - Don't over-engineer the updates

3. **Be Thorough**:
   - Read the entire existing design discussion before making changes
   - Research code patterns if changes require new technical understanding
   - Ensure updated sections maintain quality standards
   - Verify design questions still have clear decisions and rationale

4. **Be Interactive**:
   - Confirm understanding before making changes
   - Show what you plan to change before doing it
   - Allow course corrections
   - Don't disappear into research without communicating

5. **Be Practical**:
   - Focus on changes that affect design decisions and implementation
   - Consider migration and rollback implications
   - Think about edge cases introduced by changes
   - Keep "What We're NOT Doing" accurate

6. **Track Progress**:
   - Use TodoWrite to track update tasks if complex
   - Update todos as you complete research
   - Mark tasks complete when done

7. **No Open Questions**:
   - If the requested change raises questions, ASK
   - Research or get clarification immediately
   - Do NOT update the design discussion with unresolved questions
   - Every design decision must be clear before finalizing

## Sub-task Spawning Best Practices

When spawning research sub-tasks:

1. **Only spawn if truly needed** - don't research for simple changes
2. **Spawn multiple tasks in parallel** for efficiency
3. **Each task should be focused** on a specific area
4. **Provide detailed instructions** including:
   - Exactly what to search for
   - Which directories to focus on
   - What information to extract
   - Expected output format
5. **Be EXTREMELY specific about directories**:
   - If the ticket mentions "WUI", specify `humanlayer-wui/` directory
   - If it mentions "daemon", specify `hld/` directory
   - Never use generic terms like "UI" when you mean "WUI"
   - Include the full path context in your prompts
6. **Request specific file:line references** in responses
7. **Wait for all tasks to complete** before synthesizing
8. **Verify sub-task results** - if something seems off, spawn follow-up tasks

## Example Interaction Flows

**Scenario 1: User provides everything upfront**
```
User: /iterate-design-discussion thoughts/shared/tasks/checkpoint-recover/2025-10-16-design-discussion.md - revisit Q2, Option B is better because we now have the new caching layer
Assistant: [Reads design discussion, researches caching layer, updates Q2 decision]
```

**Scenario 2: User provides just the file**
```
User: /iterate-design-discussion thoughts/shared/tasks/checkpoint-recover/2025-10-16-design-discussion.md
Assistant: I've found the design discussion. What changes would you like to make?
User: Add a new design question about error recovery strategy
Assistant: [Proceeds with update]
```

**Scenario 3: User provides no arguments**
```
User: /iterate-design-discussion
Assistant: Which design discussion would you like to update? Please provide the path...
User: thoughts/shared/tasks/checkpoint-recover/2025-10-16-design-discussion.md
Assistant: I've found the design discussion. What changes would you like to make?
User: Narrow the scope - remove the migration piece from desired end state
Assistant: [Proceeds with update]
```
