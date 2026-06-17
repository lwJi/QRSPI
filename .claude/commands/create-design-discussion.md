---
description: Interactive design discussion to explore approaches and make design decisions with thorough research
model: opus
---

# Design Discussion

You are tasked with facilitating an interactive, iterative design discussion that explores approaches, weighs trade-offs, and reaches clear design decisions. You should be skeptical, thorough, and work collaboratively with the user to produce high-quality technical specifications.

## Initial Response

When this command is invoked:

1. **Check if a task directory was provided**:
   - If a task directory path is provided, skip the default message
   - Immediately read all the files within the task directory FULLY
   - Begin the research process

2. **If no task directory provided**, respond with:
```
I'll help facilitate a design discussion for your task. Please provide:
1. A task directory path (from `/create-research`) containing research artifacts
2. Or a description of what you want to design, along with any context files

I'll analyze the context and guide us through design decisions.
```

Then wait for the user's input.

## Process Steps

### Step 1: Context Gathering & Initial Analysis

1. **Read all mentioned files immediately and FULLY**:
   - `ticket.md` (e.g., `<task-dir>/ticket.md`)
   - `*-research-questions.md` - the original research questions
   - `*-research.md` - the detailed research findings
   - Any additional files mentioned
   - **IMPORTANT**: Use the Read tool WITHOUT limit/offset parameters to read entire files
   - **CRITICAL**: Read these files yourself in the main context *before* spawning any sub-tasks (Step 1.2). The "before" means ordering, not optionality — Step 1.2 is still required.
   - **NEVER** read files partially - if a file is mentioned, read it completely

2. **Spawn initial research tasks to gather context**:
   Before asking the user any questions, use specialized agents to research in parallel:

   - Use the **codebase-locator** agent to find all files related to the ticket/task
   - Use the **codebase-analyzer** agent to understand how the current implementation works

   These agents will:
   - Find relevant source files, configs, and tests
   - Identify the specific directories to focus on (e.g., if WUI is mentioned, they'll focus on humanlayer-wui/)
   - Trace data flow and key functions
   - Return detailed explanations with file:line references

3. **Read all files identified by research tasks**:
   - After research tasks complete, read ALL files they identified as relevant
   - Read them FULLY into the main context
   - This ensures you have complete understanding before proceeding

4. **Analyze and verify understanding**:
   - Cross-reference the ticket requirements with actual code
   - Identify any discrepancies or misunderstandings
   - Note assumptions that need verification
   - Determine true scope based on codebase reality

5. **Present informed understanding and focused questions**:
   ```
   Based on the ticket and my research of the codebase, I understand we need to [accurate summary].

   I've found that:
   - [Current implementation detail with file:line reference]
   - [Relevant pattern or constraint discovered]
   - [Potential complexity or edge case identified]

   Questions that my research couldn't answer:
   - [Specific technical question that requires human judgment]
   - [Business logic clarification]
   - [Design preference that affects implementation]
   ```

   Only ask questions that you genuinely cannot answer through code investigation.

### Step 2: Research & Discovery

After getting initial clarifications:

1. **If the user corrects any misunderstanding**:
   - DO NOT just accept the correction
   - Spawn new research tasks to verify the correct information
   - Read the specific files/directories they mention
   - Only proceed once you've verified the facts yourself

2. **Create a research todo list** using TodoWrite to track exploration tasks

3. **Spawn parallel sub-tasks for comprehensive research**:
   - Create multiple Task agents to research different aspects concurrently
   - Use the right agent for each type of research:

   **For deeper investigation:**
   - **codebase-locator** - To find more specific files (e.g., "find all files that handle [specific component]")
   - **codebase-analyzer** - To understand implementation details (e.g., "analyze how [system] works")
   - **codebase-pattern-finder** - To find similar features we can model after

   Each agent knows how to:
   - Find the right files and code patterns
   - Identify conventions and patterns to follow
   - Look for integration points and dependencies
   - Return specific file:line references
   - Find tests and examples

4. **Wait for ALL sub-tasks to complete** before proceeding

5. **Present findings and design options**:
   ```
   Based on my research, here's what I found:

   **Current State:**
   - [Key discovery about existing code]
   - [Pattern or convention to follow]

   **Design Options:**
   1. [Option A] - [pros/cons]
   2. [Option B] - [pros/cons]

   **Open Questions:**
   - [Technical uncertainty]
   - [Design decision needed]

   Which approach aligns best with your vision?
   ```

### Step 3: Write Artifact

After design decisions are reached:

1. **Write the design document** to `<task-dir>/YYYY-MM-DD-design-discussion.md`:

2. **Use this template structure**:

```markdown
---
date: YYYY-MM-DD
task: "[task description]"
status: complete
---

# Design Discussion: [Task Description]

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

## Design Questions

### Q1: [Topic]
[full description]

* **Option A**: **[short label]** -- [full description]
* **Option B**: **[short label]** -- [full description]

Recommendation: **Option X**
Rationale: [why]

### Q2: [Topic]
...

## References

- Original ticket: `<task-dir>/ticket.md`
- Related research: `<task-dir>/YYYY-MM-DD-research.md`
- Similar implementation: `[file:line]`
```

### Step 4: Review

1. **Present the result**:
```
I've written the design discussion to:
`<task-dir>/YYYY-MM-DD-design-discussion.md`

Review the design and let me know if you'd like to revisit any decisions, or proceed to `/create-structure-outline <task-dir>`.
```

2. **Iterate based on feedback** - be ready to:
   - Revisit design decisions
   - Adjust technical approach
   - Add/remove scope items

3. **Continue refining** until the user is satisfied

## Important Guidelines

1. **Be Skeptical**:
   - Question vague requirements
   - Identify potential issues early
   - Ask "why" and "what about"
   - Don't assume - verify with code

2. **Be Interactive**:
   - Don't write the design document in one shot
   - Get buy-in at each major decision
   - Allow course corrections
   - Work collaboratively

3. **Be Thorough**:
   - Read all context files COMPLETELY before discussing design
   - Research actual code patterns using parallel sub-tasks
   - Include specific file paths and line numbers
   - automated steps should use `make` whenever possible - for example `make -C humanlayer-wui check` instead of `cd humanlayer-wui && bun run fmt`

4. **Be Practical**:
   - Focus on decisions that affect implementation
   - Consider migration and rollback
   - Think about edge cases
   - Include "what we're NOT doing"

5. **Track Progress**:
   - Use TodoWrite to track design discussion tasks
   - Update todos as you complete research
   - Mark design discussion tasks complete when done

6. **No Open Questions in Final Design**:
   - If you encounter open questions during designing, STOP
   - Research or ask for clarification immediately
   - Do NOT write the design document with unresolved questions
   - Every design decision must be made before finalizing the design

## Common Patterns

### For Database Changes:
- Start with schema/migration
- Add store methods
- Update business logic
- Expose via API
- Update clients

### For New Features:
- Research existing patterns first
- Start with data model
- Build backend logic
- Add API endpoints
- Implement UI last

### For Refactoring:
- Document current behavior
- Plan incremental changes
- Maintain backwards compatibility
- Include migration strategy

## Sub-task Spawning Best Practices

When spawning research sub-tasks:

1. **Spawn multiple tasks in parallel** for efficiency
2. **Each task should be focused** on a specific area
3. **Provide detailed instructions** including:
   - Exactly what to search for
   - Which directories to focus on
   - What information to extract
   - Expected output format
4. **Be EXTREMELY specific about directories**:
   - If the ticket mentions "WUI", specify `humanlayer-wui/` directory
   - If it mentions "daemon", specify `hld/` directory
   - Never use generic terms like "UI" when you mean "WUI"
   - Include the full path context in your prompts
5. **Specify read-only tools** to use
6. **Request specific file:line references** in responses
7. **Wait for all tasks to complete** before synthesizing
8. **Verify sub-task results**:
   - If a sub-task returns unexpected results, spawn follow-up tasks
   - Cross-check findings against the actual codebase
   - Don't accept results that seem incorrect

Example of spawning multiple tasks:
```python
# Spawn these tasks concurrently:
tasks = [
    Task("Research database schema", db_research_prompt),
    Task("Find API patterns", api_research_prompt),
    Task("Investigate UI components", ui_research_prompt),
    Task("Check test patterns", test_research_prompt)
]
```

## Example Interaction Flow

```
User: use the create-design-discussion skill for .tasks/checkpoint-recover-subcycling
Assistant: Reading the ticket and research artifacts fully.

[Read ticket.md, *-research-questions.md, *-research.md via the Read tool]

Now spawning research agents in parallel — in a single message — to map the
current implementation before asking clarifying questions:

  - Agent(subagent_type=codebase-locator): find checkpoint/recover and
    subcycling files in the time-integration module; return file:line refs.
  - Agent(subagent_type=codebase-analyzer): trace one subcycling substep
    end-to-end; return call chain, mutated state, safe checkpoint points.

[Agents return findings]

Based on the ticket and findings, we need to support checkpoint/recover
mid-substep. Key facts the agents surfaced: <substep loop file:line>,
<existing checkpoint assumption>, <pattern that affects design>.

Questions I couldn't resolve from code: <human-judgment question>
```

Note: do not run inline Grep/Glob before the agents — that's the undertriggering failure mode this skill exists to prevent.
