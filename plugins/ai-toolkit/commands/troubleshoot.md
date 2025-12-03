---
tags: ["debugging", "investigation", "problem-solving"]
description: "5-step debugging loop for unexpected issues during implementation or standalone bug fixes"
argument-hint: "[BUG-### | TASK-### | \"description\"]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "WebSearch", "WebFetch", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/bug-workflow.md  # 5-step debugging loop, reproduction-first approach
  - docs/development/workflows/worklog-format.md  # WORKLOG entry formats
---

# /troubleshoot Command

**WHAT**: Systematic debugging using the 5-step loop from bug-workflow.md.

**WHY**: Avoid shotgun debugging. Research first, one hypothesis at a time, validate before claiming fixed.

**HOW**: Follow the 5-step debugging loop in bug-workflow.md.

## Usage

```bash
/troubleshoot "tests failing after auth changes"  # During TASK - describe the issue
/troubleshoot TASK-001     # Debug issue in specific task context
/troubleshoot BUG-007      # Equivalent to /implement BUG-007
```

## When to Use

| Situation | Command |
|-----------|---------|
| Unexpected issue during TASK implementation | `/troubleshoot "description"` or `/troubleshoot TASK-###` |
| Known bug with BUG.md created | `/troubleshoot BUG-###` or `/implement BUG-###` |
| Tests failing, unclear why | `/troubleshoot` |

## Execution

### Mode 1: During TASK Implementation

When something unexpected breaks while implementing a TASK:

1. **Read context**: Current TASK.md, PLAN.md, WORKLOG.md
2. **Execute 5-step loop** from bug-workflow.md
3. **Document** in current WORKLOG.md
4. **Continue** with TASK implementation

### Mode 2: Standalone Bug (BUG-###)

Equivalent to `/implement BUG-###`:

1. **Read context**: BUG.md, PLAN.md, WORKLOG.md
2. **Execute 5-step loop** from bug-workflow.md
3. **Document** in WORKLOG.md with root cause
4. **Complete** with `/complete BUG-###`

## The 5-Step Loop

**See bug-workflow.md for complete details.**

```
Research → Hypothesize → Implement → Test → Document
    ↑                                         │
    └─────────── (if not fixed) ──────────────┘
```

### Quick Reference

1. **Research** (auto-invoke research-specialist for external knowledge):
   - **Check first**: `docs/project/research/` for existing research docs
   - **Context7** for framework/library docs
   - **Spawn research-specialist** for unfamiliar errors, patterns, or technologies
   - Don't guess - research first
2. **Hypothesize** - ONE theory with debug plan
3. **Implement** - Fix + liberal debug logging
4. **Test** - Run tests, verify logs, confirm fix
5. **Document** - WORKLOG entry with hypothesis/findings/result

### Key Rules

- Research BEFORE guessing
- ONE hypothesis at a time
- Liberal debug logging (`console.log('[Component] State:', data)`)
- NEVER claim "fixed" without tests passing
- Rollback on failure before next attempt

## WORKLOG Entry

```markdown
## 2025-11-26 - Troubleshooting Loop 1

**Hypothesis:** Query fires before auth completes

**Debug findings:**
- isLoading was true when query executed
- Auth state not propagating to component

**Implementation:** Added auth state check

**Result:** ✓ Fixed - 47/47 tests passing
```

## Error Handling

| Situation | Action |
|-----------|--------|
| Hypothesis fails | Rollback, document, return to Step 1 |
| 3+ failed loops | Pause, ask user for context |
| Tests pass but issue persists | More logging, manual verification |

## Integration

**During TASK:** Documents in current WORKLOG, returns to `/implement`

**For BUG:** Same as `/implement BUG-###`, follows bug-workflow.md
