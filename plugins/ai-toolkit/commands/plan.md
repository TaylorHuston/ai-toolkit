---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for tasks, bugs, and spikes"
argument-hint: "### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs", "WebSearch", "WebFetch"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md    # Plan methodology, phase structure, complexity scoring
  - docs/development/workflows/task-workflow.md   # Task TDD phases
  - docs/development/workflows/bug-workflow.md    # Bug reproduction-first phases
  - docs/development/workflows/spike-workflow.md  # Spike exploration planning
  - docs/development/templates/plan-template.md   # Plan file structure
---

# /plan Command

**WHAT**: Create PLAN.md with phase-based breakdown through analysis and research.

**HOW**: Detects issue type from file (TASK.md/BUG.md/SPIKE.md) and generates appropriate plan structure.

## Usage

```bash
/plan 001         # Detects type from file, creates appropriate PLAN.md
/plan 003         # If SPIKE.md exists → creates PLAN-1.md, PLAN-2.md
/plan PROJ-123    # Jira: Fetches issue, creates PLAN.md locally
```

## Issue Type Detection

**Reads issue file to determine type (not ID prefix):**

```bash
# In pm/issues/###-name/ directory:
if [ -f "TASK.md" ]; then type="task"    # TDD phases
elif [ -f "BUG.md" ]; then type="bug"    # Reproduction-first
elif [ -f "SPIKE.md" ]; then type="spike" # Exploration plans
fi
```

| Issue Type | Plan Structure | File Created |
|------------|---------------|--------------|
| Task (TASK.md) | TDD phases (RED/GREEN/REFACTOR) | PLAN.md |
| Bug (BUG.md) | Reproduction-first phases | PLAN.md |
| Spike (SPIKE.md) | Exploration phases per approach | PLAN-1.md, PLAN-2.md, ... |

## Process

### For Task/Bug

1. **Load**: Read TASK.md/BUG.md, parent SPEC (if exists), similar WORKLOGs
2. **Analyze**: Sequential thinking, research via Context7/web search
3. **Generate**: Phase breakdown per workflow guidelines
   - **Task**: TDD phases (X.RED/X.GREEN/X.REFACTOR) per task-workflow.md
   - **Bug**: Reproduction → Fix → Harden phases per bug-workflow.md
4. **Review**: code-architect (always), security-auditor (if security-relevant)
5. **Present**: Phases, scenario coverage, complexity score, research summary

### For Spike (Exploration)

1. **Load**: Read SPIKE.md (questions, success criteria)
2. **Ask**: "How many approaches do you want to explore?" → N
3. **Gather**: For each approach:
   - "Describe approach N?" → User describes
   - "What aspects to evaluate?" → Performance, complexity, etc.
4. **Generate**: Create PLAN-N.md for each approach
   - Exploration phases (not TDD)
   - Focus on answering questions
   - Include spike reminder banner and branch reference
5. **Present**: Summary of all exploration plans

## Task Plan Example

```markdown
# Implementation Plan: 001 User Login Flow

## Phase 1 - User can log in

### 1.RED - Write Failing Tests
- [ ] 1.1 Write login tests (valid credentials, invalid, locked account)
- [ ] 1.2 [CHECKPOINT] Tests FAIL

### 1.GREEN - Implement
- [ ] 1.3 Implement login endpoint
- [ ] 1.4 [CHECKPOINT] Tests PASS

### 1.REFACTOR - Clean Up
- [ ] 1.5 Refactor, review >= 90
- [ ] 1.6 Commit phase

## Scenario Coverage
✓ SPEC-001 Scenario 1: "User logs in" → Phase 1

Complexity: 8 points (Medium)
```

## Bug Plan Example

```markdown
# Implementation Plan: 002 Login Safari Crash

## Phase 1 - Reproduce Failure

### 1.RED - Write Reproduction Test
- [ ] 1.1 Create Safari-specific test
- [ ] 1.2 [CHECKPOINT] Test FAILS (proves bug exists)

## Phase 2 - Implement Fix

### 2.GREEN - Fix the Bug
- [ ] 2.1 Investigate root cause
- [ ] 2.2 Implement fix
- [ ] 2.3 [CHECKPOINT] Reproduction test PASSES
- [ ] 2.4 [CHECKPOINT] No regressions

### 2.REFACTOR - Clean Up
- [ ] 2.5 Refactor, review >= 90
- [ ] 2.6 Commit fix
```

## Spike Plan Example

Creates multiple files with branch references:

**PLAN-1.md:**
```markdown
# 003 Exploration Plan 1: GraphQL

> **⚠️ SPIKE EXPLORATION**
> Code will be committed to spike branch, not merged to main.
> Track discoveries in WORKLOG-1.md.
> Branch: spike/003/plan-1

## Exploration Goal
Evaluate GraphQL for our API needs.

## Phase 1: Setup (1 hour)
- [ ] Install dependencies
- [ ] Create basic schema

## Phase 2: Performance Testing (2 hours)
- [ ] Run benchmarks
- [ ] Document in WORKLOG-1.md
```

## Spike Interaction Example

```
User: /plan 003

AI: Issue 003 (spike): "GraphQL vs REST for our API?"

    How many approaches do you want to explore?

User: 2

AI: Describe approach 1:

User: GraphQL with Apollo Server - evaluate schema design,
     query flexibility, and caching

AI: Describe approach 2:

User: REST with Express - compare setup simplicity,
     HTTP caching, and team familiarity

AI: Creating exploration plans...

    ✓ PLAN-1.md: GraphQL with Apollo Server
      Branch: spike/003/plan-1
      - Phase 1: Setup (1h)
      - Phase 2: Query implementation (2h)
      - Phase 3: Performance testing (1h)

    ✓ PLAN-2.md: REST with Express
      Branch: spike/003/plan-2
      - Phase 1: Setup (30min)
      - Phase 2: Endpoint implementation (2h)
      - Phase 3: Performance testing (1h)

    Next: /implement 003 (will ask which plan)
```

## Integration

```
/issue → /plan {ID} → /implement {ID} → /complete {ID}
```

**Creates:**
- Task/Bug: `pm/issues/###-*/PLAN.md`
- Spike: `pm/issues/###-*/PLAN-1.md`, `PLAN-2.md`, etc.
