---
description: Generate objective research questions about how the current codebase works, from a task description.
model: opus
---

# Create Research Questions

Generate objective, fact-finding research questions about how the current codebase works, from a task description. Each question should be answerable purely by reading existing code — no design judgment, no "what changes are needed." These questions feed into `/create-research`, which spawns parallel sub-agents to investigate each one.

## Initial Response

When this command is invoked:

1. **If a task description, file path, or task directory was provided**: read it and begin. When reading from a task directory, read only artifacts inside that directory -- never traverse upward or into siblings.
2. **If no parameters provided**, ask for a task description and any relevant context files, then wait.

## Process

### Step 1: Understand the Task

1. **Read all mentioned files fully** (tickets, docs, context). When reading from a task directory, stay within that directory.
2. **Identify key areas** of the codebase likely involved and note any ambiguities.
3. **If significant ambiguities exist**, ask 1-2 clarifying questions before scanning. Minor ambiguities become research questions instead.

### Step 2: Lightweight Codebase Scan

This step is deliberately lightweight. Your goal is only to surface enough specific identifiers (function names, files, data structures) to write concrete questions -- the sub-agents spawned by `/create-research` do the actual investigation. Time spent here reading deeply is wasted work they will redo.

Scan enough to name specific functions, data structures, and code paths in your questions -- but stop before you start answering them. Concrete stop rule: once you can name the entities a question will reference, move on; do not open sibling files, callers, or callees to confirm behavior -- that is the sub-agent's job.

Delegate file discovery to **`codebase-locator` agents** rather than running Grep/Glob/LS yourself. Each agent returns files pre-categorized by purpose (implementation, tests, config, types, entry points), and their result sets stay out of your main context -- freeing you to read only the handful of files that matter for writing questions. Default to spawning `codebase-locator` agents for discovery; only fall back to a single direct Grep/Glob/LS call for a trivial lookup (e.g., a known filename).

1. **Spawn multiple `codebase-locator` agents in parallel**, one per distinct code area the task likely touches (e.g., one for the auth layer, one for the rate-limiter, one for the API surface). Issue all the spawn calls in a single message so they run concurrently -- a serial chain wastes wall-clock time and tends to bias each prompt toward what the previous agent returned. Read each agent's categorized output to pick which files to open.
2. **Skim 2-5 key files** from the locators' reports -- entry points, configuration, or a representative call site. Skim for identifiers and structure, not behavior; if you find yourself tracing logic or reading a file top-to-bottom, stop. For new-functionality tasks, a quick look at adjacent conventions and test infrastructure is enough.
3. **While skimming, note only what you need to write specific questions**:
   - Key code paths a sub-agent would need to trace
   - Data structures and state flowing through those paths
   - Assumptions and invariants the current code relies on
   - Boundaries between components (where one sub-agent's scope ends and another's begins)

### Step 3: Generate Research Questions

Every question should be answerable with "the code does X" — never with "we should do Y." If you catch yourself writing "what would need to change," reframe as "how does this currently work." The design phase (later in the QRSPI pipeline) is where change-oriented thinking belongs.

Based on your scan, generate a flat bullet list of research questions. Each question has two parts:

```
- **<Question>** <Semicolon-separated directive phrases.>
```

The bold part is the core question. The directives after it are telegraphic verb-phrases (not full sentences) telling the sub-agent what to trace or enumerate, joined by semicolons. **Keep each bullet under 50 words total.**

**Example — good (compact):**
```
- **How does `RouterConfig.resolve()` select handler chains?** Trace call path through middleware registration; list filter predicates that narrow the handler set.
```

**Example — bad (mixes objective with design judgment):**
```
- **How does `RouterConfig.resolve()` select handler chains, and what changes are needed to support priority overrides?** Trace call path through middleware registration; identify what would need to be modified to allow runtime priority changes.
```

#### Quality criteria

Each question must be:
- **Specific**: names at least one code entity from your scan
- **Objective**: answerable purely by reading existing code — no judgment, no speculation about future changes
- **Factual**: asks what the code *does*, not what it *should do* or what *would need to change*
- **Scoped**: a single sub-agent can research it independently

Skip questions you already answered during the scan. No unbounded "how does the system work" questions.

**Red flags — rewrite any question containing these patterns:**
- "what changes are needed" / "what would need to be modified"
- "would X be sufficient" / "is X enough"
- "what additional X would be needed"
- "should" / "could" / "would" (when implying design decisions)

Instead, ask what the code currently does. The design phase will use these factual answers to determine what changes are needed.

#### Directive style

Use verb-phrase fragments, not sentences. Directive verbs: Trace, Identify, List, Enumerate, Walk, Map, Compare. Avoid vague verbs like "explore," "investigate," or "understand." Avoid forward-looking verbs like "determine what needs to change" or "assess whether." Join directives with semicolons.

At least 1-2 questions should map the assumptions, invariants, and preconditions the current code relies on — these are the facts a later design phase will need to evaluate.

#### Question count

At most 9 questions. Narrow tasks (1-2 files): 3-4. Cross-cutting changes: 7-9. Each question spawns a sub-agent, so cut "nice to know" — keep only "need to know." Merge overlapping questions. Use category thinking for coverage (architecture, data flow, integration, tests).

#### Self-review before writing

Before moving to Step 4, re-read your draft questions and rewrite any that trip the red flags above (design-judgment verbs, "what would need to change," compound "and"-joined questions). If a question survives only by being softened, cut it.

### Step 4: Write Artifact

1. **Determine the task directory**:
   - If provided, use it
   - Otherwise, auto-generate: `.tasks/<kebab-case-3-5-words>/`

2. **Write** to `<task-dir>/YYYY-MM-DD-research-questions.md`:

```markdown
---
date: YYYY-MM-DD
task: "<brief task description>"
status: complete
---

# Research Questions: <Task Description>

## Input Query
<Original task description>

## Research Questions

- **<question>** <research directives>

- **<question>** <research directives>
```

3. **Present**: State the file path, summarize the questions, and suggest reviewing before running `/create-research <task-dir>`.
