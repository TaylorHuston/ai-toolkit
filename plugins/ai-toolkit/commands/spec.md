---
tags: ["workflow", "spec", "feature-spec", "project-management", "conversational"]
description: "Create local feature specifications through natural language conversation"
argument-hint: "[SPEC-### | --epic PROJ-###]"
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
/spec                 # Start conversation (create new or work with existing)
/spec SPEC-###        # Work with specific spec
/spec --epic PROJ-### # Create spec from Jira epic (requires Jira integration)
```

## Execution Flow

**Before you start**: Read pm-guide.md for spec creation workflows, file formats, and task suggestion strategies. For Jira mode, read jira-integration.md.

### High-Level Steps

1. **Determine Mode**
   - No arguments: Ask "Create new or work on existing?"
   - SPEC-### provided: Refinement mode
   - --epic PROJ-###: Fetch Jira epic and pre-populate

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

5. **Optional Task Suggestions**
   - Suggest tasks based on spec scope
   - Interactive: Which to create? (custom/stop)
   - Per pm-guide.md task suggestion strategy

6. **Optional Jira Epic**
   - Ask: Create corresponding Jira epic?
   - If yes: Run /jira-epic --spec SPEC-###

**See pm-guide.md "Spec Creation Workflows" for complete conversational flow patterns.**

## Workflow Patterns

**Local-first** (recommended):
```
/spec → /jira-epic --spec SPEC-### → /plan TASK-###
```

**Jira-first**:
```
/spec --epic PROJ-### → /plan TASK-###
```

**Local-only**:
```
/spec → /plan TASK-###
```

## Error Handling

**--epic without Jira**: Enable Jira in CLAUDE.md or omit --epic flag
**Epic not found**: Verify epic exists and you have access
**MCP unavailable**: Fallback to manual creation

See jira-integration.md for complete error scenarios and solutions.
