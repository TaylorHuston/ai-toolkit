---
tags: ["workflow", "development", "execution"]
description: "Execute specific implementation phases from task plans with test-first enforcement"
argument-hint: "TASK-### PHASE | TASK-### --next | --next | TASK-### --task | --task"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # Phase execution protocol, test-first loop, progress tracking
  - docs/development/workflows/worklog-format.md  # WORKLOG entry formats
  - docs/development/workflows/development-loop.md  # Quality gates and enforcement
  - docs/development/workflows/git-workflow.md  # Branch creation and verification
  - docs/development/workflows/agent-coordination.md  # Agent handoff patterns
---

# /implement Command

**WHAT**: Execute implementation phases with mandatory test-first loop enforcement and intelligent agent coordination.

**WHY**: Structured execution with strict quality gates ensures robust code, prevents regressions, and maintains clean git history.

**HOW**: See pm-guide.md for phase execution protocol and test-first loop. See development-loop.md for quality gates. See agent-coordination.md for handoff patterns.

## Usage

```bash
/implement TASK-001 1.1    # Execute specific phase
/implement BUG-003 2.2     # Execute bug fix phase
/implement PROJ-123 1.1    # Execute Jira issue phase
/implement TASK-001 --next # Auto-find next uncompleted phase
/implement --next          # Auto-detect task + next phase
/implement TASK-001 --task # Execute all remaining phases
/implement --task          # Auto-detect task + execute all
```

## Execution Flow

**Before you start**: Read pm-guide.md, worklog-format.md, development-loop.md, git-workflow.md for complete workflow rules.

### High-Level Steps

1. **Parse & Validate**
   - Parse issue ID and phase number (or auto-detect)
   - Locate issue directory: `pm/issues/{ISSUE-ID}-*/`
   - Read TASK.md/BUG.md, PLAN.md
   - Create WORKLOG.md if doesn't exist (with Phase Commits section header)
   - Read WORKLOG.md for context

2. **Branch Management**
   - Check current branch
   - Create feature/bugfix branch if needed per git-workflow.md
   - Non-blocking warnings if branch mismatch

3. **Spawn Domain Agent**
   - Select agent based on phase type (frontend/backend/database/etc.)
   - Provide strategic objective from PLAN.md (WHAT to build)
   - Agent decides HOW based on WORKLOG context and current codebase
   - **Mandatory**: Agent follows test-first loop from pm-guide.md

4. **Enforce Quality Gates**
   - Tests written and failing (red)
   - Tests passing after implementation (green)
   - Code review score ≥90
   - All 10 quality gates from development-loop.md must pass

5. **Track Progress**
   - Write WORKLOG.md entry per worklog-format.md
   - Commit changes per git-workflow.md
   - Update WORKLOG.md Phase Commits section with commit hash (for rollback tracking)
   - Update PLAN.md checkbox

**See pm-guide.md "Phase Execution Protocol" for complete step-by-step details.**

## Modes

**Specific phase**: `/implement TASK-001 1.1`
- Execute single phase
- User specifies exact phase number

**Next phase**: `/implement TASK-001 --next` or `/implement --next`
- Find first uncompleted phase from PLAN.md
- Show phase to user, ask confirmation
- Execute single phase

**Full task**: `/implement TASK-001 --task` or `/implement --task`
- Execute all remaining phases sequentially
- Follow test-first loop for each phase
- Stop on: error, user input needed, or all complete
- Per-phase: tests → code → review → commit → next

## Example

```
User: /implement TASK-001 --next

AI: Next uncompleted phase: 1.2 - Implement authentication logic
    Proceed? (yes/no)

User: yes

AI: Spawning backend-specialist...
    Phase 2: Implement authentication logic
    → Tests written (20 tests, all failing)
    → Implementation complete
    → Tests passing (20/20)
    → Code review: 92/100
    ✓ Phase 2 complete

    Next: /implement TASK-001 --next
```

## Agent Coordination

**Primary**: Domain specialists (backend-specialist, frontend-specialist, database-specialist, etc.)
**Supporting**: test-engineer (test-first approach), code-reviewer (quality validation)
**Conditional**: security-auditor (auto-invoked for security-relevant phases)

**Coordination method**: WORKLOG.md handoff entries per worklog-format.md

See agent-coordination.md for complete handoff patterns and agent selection criteria.

## Error Handling

- **Issue not found**: Check `pm/issues/{ID}-*/` directory exists
- **PLAN.md missing**: Run `/plan {ID}` first to create plan
- **WORKLOG.md missing**: Auto-created with Phase Commits section header on first execution
- **Phase not found**: Verify phase number exists in PLAN.md
- **Phase already complete**: Use `--next` to find next uncompleted phase
- **Quality gate failure**: Agent iterates until all gates pass

## Integration

**Workflow position**:
```
/plan TASK-### → /implement TASK-### 1.1 → /implement TASK-### 1.2 → ... → /commit
```

**Progress tracking**: Updates PLAN.md checkboxes and WORKLOG.md entries per pm-guide.md

**Branch management**: Creates/verifies branch per git-workflow.md (non-blocking)

**Jira integration**: When using PROJ-### format, auto-fetches from Jira via MCP (requires CLAUDE.md jira.enabled: true)

See pm-guide.md for complete workflow integration details.
