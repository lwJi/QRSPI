# QRSPI

The commands and agents are taken from the original [RPI](https://github.com/humanlayer/humanlayer) and are broken down according to the [talk](https://www.youtube.com/watch?v=YwZR6tc7qYg).

*I highly recommend checking out their native IDE as well: [https://www.humanlayer.com](https://www.humanlayer.com).*

## Pipeline breakdown

Ticket &rarr; **Questions** &rarr; **Research** &rarr; Design &rarr; **Structure Outline** &rarr; **Plan** &rarr; **Implemention**

## Create a repo to version control QRSPI artifacts

1. Create a repo to store the artifacts genereted during the QRSPI workflow.

2. Go to your project repo and create a soft link to the artifact repo created in Step 1.

    ```
    cd <project-repo>
    ln -s <artifact-repo> .tasks/
    ```

3. Create a new task directory inside `.tasks` for a specific task (for example `add-feature-foo`)

    ```
    mkdir .tasks/add-feature-foo/
    ```

We will refer `.tasks/add-feature-foo/` as `<task-dir>` in the following sections.

## Install QRSPI

1. Copy all the agents from `.claude/agents/*` into your `~/.claude/agents/`

2. Copy all the comments from `.claude/commands/*` into your `~/.claude/commands`

3. Use `Opus 4.8` with `high` effort

## QRSPI workflow

1. Create a `ticket.md` in `<task-dir>`.

2. Generate research questions

    ```
    use the create-research-questions skill for <task-dir>
    ```

3. Research codebase to answer the above research questions

    ```
    use the create-research skill with the following questions (write the doc to <task-dir>)

    * Q1
    * Q2
    ```

    where the questions are copied from the output artifact of Step 2. **DO NOT** copy the ticket content to keep the questions objective.

4. Design Discussion

    ```
    use the create-design-discussion skill for <task-dir>
    ```

    You can also iterate the design with

    ```
    use the iterate-design-discussion skill to update the design-discussion doc (<design-discussion-doc>)
    ```

5. Structure Outline

    ```
    use create-structure-outline skill for <task-dir>
    ```

6. Write Plan

    ```
    use the create-plan skill for <task-dir>
    ```

    You can also iterate the plan with

    ```
    use the iterate-plan skill to update the plan doc (<plan-doc>)
    ```

7. Implement Plan

    ```
    /implement-plan - PATH_TO_PLAN.md

    Please implement the plan.

    Just do phase 1, then update the plan with your progress and await further instructions and confirmation of the manual verification steps.
    ```

    ```
    /implement-plan - PATH_TO_PLAN.md
    phase 1 is done, just do phase 2, then update the plan with your progress and await further instructions and confirmation of the manual verification steps
    ```

### Important

* **ALWAYS CREATE A NEW SESSION FOR EACH STEP OF THE WORKFLOW**

* Attach the design-discussion doc into your PR to help the reviewer
