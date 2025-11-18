---
tags: ["workflow", "spec", "feature-spec", "project-management", "conversational"]
description: "Create local feature specifications through natural language conversation"
argument-hint: "[SPEC-### | --epic PROJ-### | --update SPEC-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # Spec creation workflows and file formats
  - docs/development/misc/jira-integration.md  # Jira mode (if enabled in CLAUDE.md)
---

# /spec Command

**WHAT**: Create/refine local feature specifications through conversational interaction.

**WHY**: Natural conversation ensures complete spec structure without rigid forms. Specs document WHAT to build locally, separate from PM tool tracking.

**HOW**: See pm-guide.md for spec creation workflows, file formats, and task suggestion strategies. For Jira mode, see jira-integration.md.

## Usage

```bash
/spec                   # Start conversation (create new or work with existing)
/spec SPEC-###          # Work with specific spec
/spec --epic PROJ-###   # Create spec from Jira epic (requires Jira integration)
/spec --update SPEC-### # Review and update spec based on recent development (run after task completion)
```

## Execution Flow

**Before you start**: Read pm-guide.md for spec creation workflows, file formats, and task suggestion strategies. For Jira mode, read jira-integration.md.

### High-Level Steps

1. **Determine Mode**
   - No arguments: Ask "Create new or work on existing?"
   - SPEC-### provided: Refinement mode
   - --epic PROJ-###: Fetch Jira epic and pre-populate
   - --update SPEC-###: Review mode (analyze recent development and update spec)

2. **Load Context**
   - Read pm/templates/spec.md for structure
   - Glob pm/specs/SPEC-*.md for next number
   - If --epic: Fetch from Jira via Atlassian MCP

3. **Conversational Creation**
   - Main goal, primary users
   - Acceptance scenarios (2-5 Given-When-Then)
   - Success criteria, OUT of scope
   - Follow pm/templates/spec.md structure

4. **Create Spec File**
   - Write pm/specs/SPEC-###-<name>.md
   - Mark Jira source if --epic used

5. **Task Creation** (interactive)
   - Suggest 2-3 foundational tasks based on spec scope
   - Interactive loop: Which to create? (yes/all/custom/stop)
   - For each selected task:
     - Create `pm/issues/TASK-###-name/` directory
     - Create `TASK.md` from `templates/task.md` template
     - Link task → spec (frontmatter: `spec: SPEC-###`)
     - Update spec → task reference (Tasks section)
   - Per pm-workflows.md task suggestion strategy

6. **Optional Jira Epic**
   - Ask: Create corresponding Jira epic?
   - If yes: Run /jira-epic --spec SPEC-###

**See pm-guide.md "Spec Creation Workflows" for complete conversational flow patterns.**

## Update Mode (`--update SPEC-###`)

**When to use:** After completing tasks to sync spec with implementation reality.

**Workflow:**

1. **Load Context**
   - Read SPEC-###-<name>.md
   - Read all linked TASK-###/TASK.md files
   - Read all linked TASK-###/WORKLOG.md files (completed tasks)
   - Read recent git commits for the spec's feature branch

2. **Analyze Development Activity**
   - What was actually implemented vs planned?
   - Were there scope changes during implementation?
   - Did acceptance scenarios change?
   - Were new edge cases discovered?
   - Did any requirements change?

3. **Review Incomplete Tasks**
   - Read all tasks with status: `todo` or `in_progress`
   - Based on completed work, do these tasks still make sense?
   - Should any be:
     - Modified (scope changed based on discoveries)
     - Removed (no longer needed)
     - Split (too large based on actual complexity)
     - Merged (smaller than expected)
   - Suggest changes interactively

4. **Update Spec**
   - Revise description if scope changed
   - Update/add acceptance scenarios based on discoveries
   - Update definition of done if needed
   - Mark completed tasks as done
   - Add new tasks if gaps discovered
   - Update "Out of Scope" section if boundaries changed

5. **Document Changes**
   - Add update note to spec with timestamp
   - Summary of what changed and why
   - Link to completed tasks that informed changes

**Example:**
```bash
# After completing TASK-001 and TASK-002 for SPEC-001
/spec --update SPEC-001

→ Reads SPEC-001-user-authentication.md
→ Reads completed TASK-001/WORKLOG.md, TASK-002/WORKLOG.md
→ Reviews remaining TASK-003, TASK-004, TASK-005

AI: "During TASK-001, you implemented OAuth in addition to email/password.
     Should we update the spec to include OAuth in the description?"

User: "Yes"

AI: "TASK-004 was for 'password complexity rules', but you already
     implemented basic validation in TASK-001. Should we:
     1. Remove TASK-004 (no longer needed)
     2. Modify TASK-004 to focus on advanced rules only
     3. Keep as-is"

User: "2"

→ Updates SPEC-001 with OAuth in description
→ Updates TASK-004 description to "Advanced password rules (strength meter, common password detection)"
→ Adds update note with timestamp
```

**Integration with Workflow:**
```bash
/implement TASK-001 --task  # Complete task
/quality                     # Verify quality
/spec --update SPEC-001      # Sync spec with reality
/plan TASK-002              # Plan next task with updated context
```

## Workflow Patterns

**Local-first** (recommended):
```
/spec → /jira-epic --spec SPEC-### → /plan TASK-### → /implement → /spec --update SPEC-###
```

**Jira-first**:
```
/spec --epic PROJ-### → /plan TASK-### → /implement → /spec --update SPEC-###
```

**Local-only**:
```
/spec → /plan TASK-### → /implement → /spec --update SPEC-###
```

**Iterative development cycle**:
```
/spec SPEC-001                    # Create spec with tasks
/plan TASK-001 → /implement       # Complete first task
/spec --update SPEC-001           # Review and adjust remaining work
/plan TASK-002 → /implement       # Next task with updated context
/spec --update SPEC-001           # Continue iterating
```

## Error Handling

**--epic without Jira**: Enable Jira in CLAUDE.md or omit --epic flag
**Epic not found**: Verify epic exists and you have access
**MCP unavailable**: Fallback to manual creation

See jira-integration.md for complete error scenarios and solutions.
