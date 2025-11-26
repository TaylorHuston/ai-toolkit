---
tags: ["workflow", "development", "execution"]
description: "Execute specific implementation phases from task plans with test-first enforcement"
argument-hint: "TASK-### PHASE | TASK-### --next | --next | TASK-### --full | --full"
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
/implement TASK-001 --full # Execute all remaining phases
/implement --full          # Auto-detect task + execute all

# Spike exploration (no commits)
/implement --spike SPIKE-001 --plan 1  # Execute spike exploration plan 1
/implement --spike SPIKE-001 --next    # Auto-find next spike plan
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

4. **Enforce TDD Checkpoints** (for phases with testable behavior)
   - **RED checkpoint**: Tests run and FAIL before implementation begins
     - If tests PASS: BLOCK - not testing new behavior
     - If tests ERROR: BLOCK - fix test bugs first
     - Document in WORKLOG: "RED: X tests failing - [reason]"
   - **GREEN checkpoint**: Tests PASS after implementation
   - **REFACTOR loop**: Code review → if < 90, iterate → review ≥ 90 to exit
   - All quality gates from quality-gates.md must pass

5. **Track Progress**
   - Write WORKLOG.md entry per worklog-format.md
   - Commit changes per git-workflow.md
   - Update WORKLOG.md Phase Commits section with commit hash (for rollback tracking)
   - Update PLAN.md checkbox

**See pm-guide.md "Phase Execution Protocol" for complete step-by-step details.**

### Spike Exploration (--spike flag)

**When to use:** `/implement --spike SPIKE-001 --plan 1` for exploring technical approaches without committing code.

**Behavior differences with --spike flag:**

1. **NO Git Commits**
   - All code changes tracked in WORKLOG-N.md only
   - Phase Commits section tracks prototype commits locally
   - No branch creation/switching
   - Reminder displayed: "⚠️ SPIKE EXPLORATION - Code will NOT be committed"

2. **Prototype Directory**
   - Code goes in `pm/issues/SPIKE-###/prototype/` directory
   - Organized by approach (e.g., `prototype/graphql/`, `prototype/rest/`)
   - All code is exploratory - may be discarded after spike

3. **WORKLOG Tracking**
   - Uses WORKLOG-N.md (numbered by plan: WORKLOG-1.md, WORKLOG-2.md)
   - Tracks discoveries, not deliverables
   - Documents setup time, surprises, questions surfaced
   - Performance data and complexity observations

4. **Quality Gates**
   - Test-first loop NOT enforced (exploration code)
   - Code review NOT required (throwaway prototypes)
   - Focus on answering questions, not production quality

5. **PLAN.md Reminder**
   - SPIKE PLAN-N.md files include reminder at top:
   ```markdown
   > **⚠️ SPIKE EXPLORATION**
   > This is exploratory work. Code will NOT be committed to production.
   > Use `/implement --spike SPIKE-001 --plan 1` to execute.
   ```

**Example workflow:**
```bash
/implement --spike SPIKE-001 --plan 1  # Explore GraphQL approach
# ... prototype GraphQL server, document findings in WORKLOG-1.md
/implement --spike SPIKE-001 --plan 2  # Explore REST approach
# ... prototype REST server, document findings in WORKLOG-2.md
/spike --complete SPIKE-001            # Generate SPIKE-SUMMARY.md
```

**See:** spike-workflow.md for complete spike exploration workflows and templates.

## Task-Specific Scripts

**When you need temporary scripts during implementation:**

**Use task directory** (`pm/issues/TASK-###-name/scripts/`):
- ✅ One-off database migration scripts
- ✅ Test data generation scripts
- ✅ Debug/investigation scripts
- ✅ Setup scripts specific to this task
- ✅ Temporary automation for this feature

**Use root scripts directory** (`scripts/` at project root):
- ✅ Universal utilities used across multiple tasks
- ✅ Build/deployment scripts
- ✅ Database seeding for all environments
- ✅ CI/CD helper scripts
- ✅ Scripts that will be maintained long-term

**Why task-specific scripts?**
- Easy cleanup after task completion
- Clear context (script tied to specific task)
- No root directory clutter
- Easy to find debugging scripts later

**Example structure:**
```
pm/issues/TASK-001-user-auth/
├── TASK.md
├── PLAN.md
├── WORKLOG.md
└── scripts/
    ├── generate-test-users.sh      # Create test data
    ├── migrate-passwords.js        # One-time migration
    └── debug-jwt-validation.js     # Debug script
```

## Modes

**Specific phase**: `/implement TASK-001 1.1`
- Execute single phase
- User specifies exact phase number

**Next phase**: `/implement TASK-001 --next` or `/implement --next`
- Find first uncompleted phase from PLAN.md
- Show phase to user, ask confirmation
- Execute single phase

**Full execution**: `/implement TASK-001 --full` or `/implement --full`
- Execute all remaining phases sequentially
- Follow test-first loop for each phase
- Stop on: error, user input needed, or all complete
- Per-phase: tests → code → review → commit → next

## Example

```
User: /implement TASK-001 --next

AI: Next uncompleted phase: 1.RED - Write failing tests for user login
    Proceed? (yes/no)

User: yes

AI: Spawning test-engineer...
    Phase 1.RED: Write failing tests for user login
    → Tests written (8 test cases)
    → Running tests...
    → [RED CHECKPOINT] 8 tests FAILED as expected ✓
    ✓ Phase 1.RED complete

    Next: /implement TASK-001 --next

---

User: /implement TASK-001 --next

AI: Next uncompleted phase: 1.GREEN - Implement user login
    Proceed? (yes/no)

User: yes

AI: Spawning backend-specialist...
    Phase 1.GREEN: Implement user login
    → Implementation complete
    → Running tests...
    → [GREEN CHECKPOINT] 8/8 tests PASS ✓
    ✓ Phase 1.GREEN complete

    Next: /implement TASK-001 --next

---

User: /implement TASK-001 --next

AI: Next uncompleted phase: 1.REFACTOR - Clean up user login
    Proceed? (yes/no)

User: yes

AI: Spawning backend-specialist...
    Phase 1.REFACTOR: Clean up user login
    → Refactoring...
    → Tests still passing (8/8) ✓
    → Code review: 85/100 (needs error handling improvement)
    → Addressing feedback...
    → Tests still passing (8/8) ✓
    → Code review: 92/100 ✓
    → [EXIT GATE] Review ≥90, committing...
    ✓ Phase 1.REFACTOR complete

    Next: /implement TASK-001 --next (Phase 2.RED)
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
