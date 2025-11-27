---
last_updated: "2025-11-26"
description: "Bug fix workflow with reproduction-first approach and 5-step debugging loop"
---

# Bug Workflow

**Referenced by:** `/issue`, `/plan`, `/implement`, `/complete`, `/troubleshoot`

Structured bug fixing with reproduction-first testing and systematic debugging.

---

## When to Use

| Situation | Workflow | Why |
|-----------|----------|-----|
| Something is broken | **BUG** | Prove it's broken, then fix |
| Unexpected issue during TASK | **BUG** (or `/troubleshoot`) | Same methodology applies |
| New feature needed | TASK | Different acceptance criteria |
| Technical uncertainty | SPIKE | Explore before committing |

**Decision Rule:** Can you reproduce the failure? → BUG. Can't reproduce? → Investigate first with 5-step loop.

---

## Workflow Overview

```
/issue "description" → BUG.md (numeric ID, e.g., 002)
/plan ### → PLAN.md (reproduction-first phases)
/troubleshoot ### → Fix with regression test (uses 5-step loop)
/complete ### → Validate fix, document root cause
```

**Or during TASK implementation:**
```
/troubleshoot "description of what's going wrong" → Uses 5-step loop, documents in current WORKLOG
```

---

## Phase Structure

Bug fixes follow a **reproduction-first** approach:

### Phase 1: Reproduce
- Write test that reproduces the bug
  - Ask yourself why our existing tests did not catch this issue
- Test MUST FAIL (proves bug exists)
- Document exact reproduction steps

### Phase 2: Fix (uses 5-step debugging loop)
- Investigate root cause using 5-step loop
- Implement minimal fix
- Reproduction test MUST PASS
- No regressions in existing tests
- Code review ≥ 90

### Phase 3: Harden (optional)
- Add related edge case tests
- Refactor if fix reveals code smell

---

## The 5-Step Debugging Loop

**Used during Phase 2 (Fix) and by `/troubleshoot` command.**

```
Research → Hypothesize → Implement → Test → Document
    ↑                                         │
    └─────────── (if not fixed) ──────────────┘
```

### Key Rules

- **Research BEFORE guessing** (Context7 → docs → user guidance)
- **ONE hypothesis at a time** (no shotgun debugging)
- **Liberal debug logging** (console.log everywhere)
- **NEVER claim "fixed" without proof** (tests + confirmation)
- **Rollback on failure** (clean state before next try)

### Step 1: Research

**Don't guess.** Check in order:
1. Context7 for library documentation
2. Official docs for error messages
3. Ask user for relevant docs/examples

### Step 2: Hypothesize

**Form ONE hypothesis:**
- What's wrong
- Why it's happening
- What will fix it
- Where to add debug logging

### Step 3: Implement

**Apply fix with liberal debug logging:**
```typescript
console.log('[ComponentName] State:', { value1, value2 });
console.log('[functionName] Before:', data);
// ... change ...
console.log('[functionName] After:', data);
```

### Step 4: Test

1. Run automated tests
2. Check debug logs match expected flow
3. Reproduce original issue (should be fixed)
4. Check for regressions

**CRITICAL:** Never claim "fixed" without tests passing.

### Step 5: Document

Write WORKLOG entry with hypothesis, findings, and result.

### If Not Fixed

1. **Rollback changes** (`git checkout -- [files]`)
2. **Return to Step 1** with new research direction
3. **After 3+ loops:** Ask user for additional context

---

## Plan Example

```markdown
# Implementation Plan: 002 Login Safari Crash

## Phase 1 - Reproduce Failure

### 1.RED - Write Reproduction Test
- [ ] 1.1 Create test simulating Safari iOS
- [ ] 1.2 [CHECKPOINT] Test FAILS (proves bug)

## Phase 2 - Implement Fix

### 2.GREEN - Fix the Bug (5-step loop)
- [ ] 2.1 Research: Check Safari event handling docs
- [ ] 2.2 Hypothesize: Form single hypothesis
- [ ] 2.3 Implement: Fix with debug logging
- [ ] 2.4 Test: Reproduction test PASSES, no regressions
- [ ] 2.5 Document: Root cause in WORKLOG

### 2.REFACTOR - Clean Up
- [ ] 2.6 Remove debug logging (or convert to proper logging)
- [ ] 2.7 Code review ≥ 90
- [ ] 2.8 Commit fix
```

---

## Quality Gates

**All must pass before completion:**

- ✅ Reproduction test exists (fails without fix)
- ✅ Fix implemented
- ✅ Reproduction test passes
- ✅ No regressions (all existing tests pass)
- ✅ Root cause documented

---

## Root Cause Documentation

**Required for bug completion.** Document in WORKLOG:

1. **What happened** - Observed behavior
2. **Why it happened** - Technical root cause
3. **How we fixed it** - Solution approach
4. **How we prevent recurrence** - Tests added

---

## WORKLOG

Track investigation and fix in WORKLOG.md. Use **Troubleshooting format** during 5-step loop.

See `worklog-format.md` for all entry formats and examples.

---

## Severity Guidelines

| Severity | Criteria | Response |
|----------|----------|----------|
| **Critical** | Production down, data loss, security | Drop everything |
| **High** | Major feature broken | Next priority |
| **Medium** | Feature degraded, workaround exists | Normal queue |
| **Low** | Minor, cosmetic, edge case | Backlog |

---

## Completion

**`/complete ###` validates:**

- ✅ Reproduction test exists
- ✅ Reproduction test passes (with fix)
- ✅ No regressions
- ✅ Root cause documented in WORKLOG
- ✅ All tests still pass
- ✅ All linters still pass
- ✅ CHANGELOG updated

**Post-completion:** BUG.md status → `done`, ready for `/branch merge develop`

---

## Related Files

- task-workflow.md - Feature implementation workflow
- spike-workflow.md - Technical exploration workflow
- worklog-format.md - Entry formats
