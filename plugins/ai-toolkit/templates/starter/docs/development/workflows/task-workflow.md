---
last_updated: "2025-11-26"
description: "Task implementation workflow with TDD phases"
---

# Task Workflow

**Referenced by:** `/issue`, `/plan`, `/implement`, `/complete`

Structured task implementation with TDD (RED/GREEN/REFACTOR) phases.

---

## When to Use

| Situation | Workflow | Why |
|-----------|----------|-----|
| Clear requirements, can write tests | **TASK** | TDD ensures quality |
| Don't know what to test yet | SPIKE first | Explore before committing |
| Infrastructure, config, scaffolding | TASK (simple phases) | No testable behavior |

**Decision Rule:** Can you write a failing test? → TASK. Don't know what to test? → SPIKE first.

---

## Workflow Overview

```
/issue "description" → TASK.md (numeric ID, e.g., 001)
/plan ### → PLAN.md (TDD phases)
/implement ### → Execute phases with tests
/complete ### → Validate and mark done
```

---

## Phase Structure

**Phase = One Behavior = One RED/GREEN/REFACTOR cycle**

### X.RED - Write Failing Tests
1. Write tests defining expected behavior
2. Run tests - MUST fail (RED checkpoint)
3. If tests pass: STOP - not testing new behavior

### X.GREEN - Implement to Pass
4. Write minimal code to make tests pass
5. Run tests - MUST pass (GREEN checkpoint)

### X.REFACTOR - Clean Up
6. Refactor for maintainability
7. Run tests - must still pass
8. Code review - if < 90, repeat 6-8
9. Review ≥ 90 - commit phase, next behavior

---

## Plan Example

```markdown
# Implementation Plan: 001 User Login

## Phase 1 - User can log in

### 1.RED - Write Failing Tests
- [ ] 1.1 Write login tests (valid, invalid, locked)
- [ ] 1.2 [CHECKPOINT] Tests FAIL

### 1.GREEN - Implement
- [ ] 1.3 Implement login endpoint
- [ ] 1.4 [CHECKPOINT] Tests PASS

### 1.REFACTOR - Clean Up
- [ ] 1.5 Refactor, review >= 90
- [ ] 1.6 Commit phase
```

---

## Quality Gates

**All must pass before phase completion:**

- ✅ All tests pass
- ✅ Code review score ≥ 90
- ✅ Test coverage ≥ 95% (or project target)
- ✅ No critical security issues
- ✅ Acceptance criteria met

See quality-gates.md for configuration.

---

## Agent Orchestration

```
/implement ### PHASE
  ↓
1. Read phase from PLAN.md
2. Select specialist (backend, frontend, etc.)
3. Specialist executes (test → code → review loop)
4. Code-reviewer validates (score ≥ 90 to proceed)
5. Phase marked complete
```

See agent-coordination.md for patterns.

---

## WORKLOG

Track progress in WORKLOG.md. Use **Standard format** entries for phase completion.

See `worklog-format.md` for all entry formats and examples.

---

## Completion

**`/complete ###` validates:**

- ✅ All phases complete (PLAN.md checkboxes)
- ✅ Tests passing
- ✅ Linters passing
- ✅ Code review ≥ 90 on final phase
- ✅ Acceptance criteria met (TASK.md)
- ✅ WORKLOG updated
- ✅ CHANGELOG updated

**Post-completion:** TASK.md status → `done`, ready for `/branch merge develop`

---

## Related Files

- bug-workflow.md - Bug fix workflow (reproduction-first)
- spike-workflow.md - Technical exploration workflow
- quality-gates.md - Gate configuration
- agent-coordination.md - Orchestration patterns
- worklog-format.md - Entry formats
