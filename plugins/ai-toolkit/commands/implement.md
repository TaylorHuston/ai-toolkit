---
tags: ["workflow", "development", "execution"]
description: "Execute specific implementation phases from task plans with test-first enforcement"
argument-hint: "### PHASE | ### --next | --next | ### --full | --full"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md       # Phase execution protocol
  - docs/development/workflows/task-workflow.md      # TDD phases, quality gates
  - docs/development/workflows/bug-workflow.md       # Reproduction-first phases
  - docs/development/workflows/spike-workflow.md     # Spike exploration with branches
  - docs/development/workflows/worklog-format.md     # WORKLOG entry formats
  - docs/development/workflows/git-workflow.md       # Branch creation and verification
  - docs/development/workflows/agent-coordination.md # Agent handoff patterns
---

# /implement Command

**WHAT**: Execute implementation phases with test-first enforcement and agent coordination.

**HOW**: Detects issue type, follows its workflow, enforces quality gates.

**MANDATORY**: You MUST create/update WORKLOG.md for EVERY phase. Read `docs/development/workflows/worklog-format.md` for entry format. No phase is complete without a WORKLOG entry documenting what was done, decisions made, and gotchas encountered.

## Usage

```bash
/implement 001 1.1         # Execute specific phase (detects type from file)
/implement 003 2.2         # Execute bug fix phase
/implement 005             # Spike: asks which plan, creates spike branch
/implement 001 --next      # Auto-find next uncompleted phase
/implement --next          # Auto-detect issue + next phase
/implement 001 --full      # Execute all remaining phases
/implement PROJ-123 --next # Jira issue
```

## Issue Type Detection

**Reads issue file to determine type and workflow:**

```bash
# In pm/issues/###-name/ directory:
if [ -f "TASK.md" ]; then type="task"    # TDD phases
elif [ -f "BUG.md" ]; then type="bug"    # Reproduction-first
elif [ -f "SPIKE.md" ]; then type="spike" # Exploration plans
fi
```

| Issue File | Workflow | Branch Pattern |
|------------|----------|----------------|
| TASK.md | TDD (RED/GREEN/REFACTOR) | `feature/###` |
| BUG.md | Reproduction-first | `bugfix/###` |
| SPIKE.md | Exploration (per plan) | `spike/###/plan-N` |

## Execution Flow

### 1. Parse & Validate

- Parse issue ID and phase (or auto-detect)
- Locate issue directory: `pm/issues/{ID}-*/`
- Read issue file (TASK.md/BUG.md/SPIKE.md)
- Read PLAN.md (or PLAN-N.md for spikes)
- Create WORKLOG.md if missing (with Phase Commits header)

### 2. Type-Specific Handling

**For TASK/BUG:**
- Create feature/bugfix branch if needed
- Execute phase with TDD checkpoints
- Follow quality gates per workflow

**For SPIKE (see below):**
- Ask which plan to explore
- Create spike branch
- Execute without quality gates

### 3. Agent Coordination

Select agent based on phase domain:
- `frontend-specialist`, `backend-specialist`, `database-specialist`
- `test-engineer` for RED phases
- `code-reviewer` for REFACTOR phases

### 4. TDD Checkpoints (TASK/BUG)

- **RED**: Tests FAIL before implementation
- **GREEN**: Tests PASS after implementation
- **REFACTOR**: Review ≥90 to complete

### 5. Track Progress (MANDATORY for every phase)

1. **Create WORKLOG.md** if it doesn't exist (with Phase Commits header)
2. **Write WORKLOG entry** following `docs/development/workflows/worklog-format.md`:
   - Use Standard Format for implementation work
   - Include: Completed, Key decisions, Gotchas, Files
   - Run `date '+%Y-%m-%d %H:%M'` for timestamp
3. **Update Phase Commits section** with commit hash
4. **Check off PLAN.md checkbox**
5. **Commit changes**

⚠️ A phase is NOT complete until WORKLOG.md is updated.

---

## SPIKE Implementation

**When issue type is SPIKE, behavior changes significantly.**

### Step 1: Select Plan

```
Which plan do you want to explore?

Available plans:
  1. PLAN-1.md: GraphQL with Apollo Server
  2. PLAN-2.md: REST with Express

Enter plan number: _
```

### Step 2: Create Spike Branch

```bash
# From current branch (usually develop)
git checkout -b spike/005/plan-1
```

### Step 3: Execute Exploration

**Differences from TASK/BUG:**

| Aspect | TASK/BUG | SPIKE |
|--------|----------|-------|
| TDD checkpoints | Required | Not enforced |
| Code review | Required (≥90) | Not required |
| Commits | To feature branch | To spike branch |
| WORKLOG | WORKLOG.md | WORKLOG-N.md |
| Branch merge | Yes (to develop) | Never |

### Step 4: Track Findings

- Update WORKLOG-N.md on spike branch
- Document discoveries, surprises, benchmarks
- Track time spent vs time box

### Example SPIKE Flow

```
User: /implement 005

AI: Issue 005 (spike): "GraphQL vs REST for our API?"

    Which plan do you want to explore?
    1. PLAN-1.md: GraphQL with Apollo Server
    2. PLAN-2.md: REST with Express

User: 1

AI: Creating branch spike/005/plan-1...

    > ⚠️ SPIKE EXPLORATION
    > Code will be committed to spike branch, not merged to main.
    > Track discoveries in WORKLOG-1.md.

    Executing PLAN-1.md Phase 1: Setup
    → Installing Apollo Server...
    → Creating basic schema...
    → Updating WORKLOG-1.md

    ✓ Phase 1 complete (1.5 hours spent, 6.5 remaining)

    Continue to Phase 2? (yes/no/stop)
```

---

## Task-Specific Scripts

**Temporary scripts go in task directory:**

```
pm/issues/001-user-auth/
├── TASK.md
├── PLAN.md
├── WORKLOG.md
└── scripts/
    ├── generate-test-users.sh
    ├── migrate-passwords.js
    └── debug-jwt-validation.js
```

**Why:** Easy cleanup, clear context, no root clutter.

**Root `scripts/`:** Only for universal utilities used across tasks.

---

## Modes

| Mode | Command | Behavior |
|------|---------|----------|
| Specific | `/implement 001 1.1` | Execute single phase |
| Next | `/implement 001 --next` | Find first uncompleted |
| Full | `/implement 001 --full` | Execute all remaining phases |
| Auto | `/implement --next` | Auto-detect issue + next |

**`--full` mode requirements:**
- WORKLOG.md entry after EACH phase (not just at the end)
- Quality gates enforced for each phase
- Phase Commits updated after each commit
- Stops if any phase fails quality gates

---

## Example (TASK)

```
User: /implement 001 --next

AI: Next: 1.RED - Write failing tests for user login
    Proceed? (yes/no)

User: yes

AI: Spawning test-engineer...
    → Tests written (8 test cases)
    → Running tests...
    → [RED CHECKPOINT] 8 tests FAILED ✓
    ✓ Phase 1.RED complete

    Next: /implement 001 --next
```

---

## Error Handling

| Error | Resolution |
|-------|------------|
| Issue not found | Check `pm/issues/{ID}-*/` exists |
| PLAN.md missing | Run `/plan {ID}` first |
| Unknown type | Verify TASK.md/BUG.md/SPIKE.md exists |
| Spike no plans | Run `/plan ###` to create plans |
| Phase complete | Use `--next` for next phase |

## Integration

```
/issue → /plan ### → /implement ### → /complete ### → /branch merge
```

**SPIKE flow:**
```
/issue → /plan ### → /implement ### → /complete ###
                         ↓
               (creates spike branch)
                         ↓
               (never merges to develop)
```
