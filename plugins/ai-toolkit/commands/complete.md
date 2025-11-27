---
tags: ["workflow", "completion", "task", "bug", "spike"]
description: "Complete any issue type per its workflow requirements"
argument-hint: "### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Glob", "Grep", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/task-workflow.md   # Task completion requirements
  - docs/development/workflows/bug-workflow.md    # Bug completion requirements
  - docs/development/workflows/spike-workflow.md  # Spike completion requirements
---

# /complete Command

**WHAT**: Complete any issue type by validating and executing its workflow's completion requirements.

**WHY**: Each issue type has different completion criteria. This command ensures all requirements are met before marking work as done.

**HOW**: Detect issue type, read its workflow's Completion section, validate/execute requirements, mark complete.

## Usage

```bash
/complete 001         # Detects type from file, runs completion workflow
/complete 002         # If BUG.md exists → bug completion
/complete 005         # If SPIKE.md exists → spike completion
/complete PROJ-123    # Jira issue completion
```

## Execution Flow

### 1. Detect Issue Type

Read issue file to determine type:

```bash
# In pm/issues/###-name/ directory:
if [ -f "TASK.md" ]; then type="task"
elif [ -f "BUG.md" ]; then type="bug"
elif [ -f "SPIKE.md" ]; then type="spike"
fi
```

### 2. Execute Type-Specific Completion

---

## Task Completion

**Reference:** task-workflow.md Completion section

### Validation Checklist

1. **All phases complete** - Every checkbox in PLAN.md checked
2. **Tests passing** - Run test suite, all green
3. **Code review ≥90** - Final phase achieved review threshold
4. **Acceptance criteria met** - All criteria in TASK.md satisfied
5. **WORKLOG updated** - Final entry documents completion

### Execution

```
Completing issue 001 (task)...

✓ PLAN.md: All phases checked (5/5)
✓ Tests: 47/47 passing
✓ Code review: 92/100 on final phase
✓ Acceptance criteria: 4/4 met
✓ WORKLOG: Final entry present

Task ready for merge. Update status? (yes/no)
```

If validation fails, report what's missing and suggest next steps.

### Final Actions

- Update TASK.md status to `done`
- Suggest: "Ready for PR. Run `/branch merge develop`"

---

## Bug Completion

**Reference:** bug-workflow.md Completion section

### Validation Checklist

1. **Reproduction test exists** - Test that fails without fix
2. **Fix implemented** - Code changes complete
3. **Reproduction test passes** - Fix resolves the issue
4. **No regressions** - All existing tests still pass
5. **Root cause documented** - WORKLOG explains why bug occurred

### Execution

```
Completing issue 002 (bug)...

✓ Reproduction test: tests/auth/login-safari.test.ts
✓ Fix implemented: src/auth/login.ts
✓ Reproduction test: PASSES with fix
✓ Regression tests: 156/156 passing
✓ Root cause: Documented in WORKLOG.md

Bug fix ready for merge. Update status? (yes/no)
```

### Final Actions

- Update BUG.md status to `done`
- Suggest: "Ready for PR. Run `/branch merge develop`"

---

## Spike Completion

**Reference:** spike-workflow.md Completion section

### Process

Unlike TASK/BUG, spike completion involves active work:

1. **Collect WORKLOGs from branches**
2. **Generate SPIKE-SUMMARY.md**
3. **Commit documentation to develop**
4. **Create follow-up actions**
5. **Clean up spike branches**

### Execution

```
Completing issue 005 (spike)...

Step 1: Collecting findings from spike branches...
  → Checking out WORKLOG-1.md from spike/005/plan-1
  → Checking out WORKLOG-2.md from spike/005/plan-2

Step 2: Generating SPIKE-SUMMARY.md...
  [Draft summary presented for review]

Step 3: Review summary
  Edit summary? (yes/no)

Step 4: Follow-up actions
  Create ADR from findings? (yes/no)
  Update related spec? (yes/no)
  Create implementation tasks? (yes/no)

Step 5: Commit to develop
  → git add pm/issues/005-*/
  → git commit -m "docs: 005 spike findings - recommend REST"

Step 6: Cleanup
  Delete spike branches? (yes/keep)
  → spike/005/plan-1
  → spike/005/plan-2
```

### Git Operations for Spike

```bash
# Ensure on develop branch
git checkout develop

# Pull WORKLOG files from spike branches
git checkout spike/005/plan-1 -- pm/issues/005-*/WORKLOG-1.md
git checkout spike/005/plan-2 -- pm/issues/005-*/WORKLOG-2.md

# Commit documentation
git add pm/issues/005-*/
git commit -m "docs: 005 spike findings - [recommendation]"

# Optional: delete spike branches
git branch -D spike/005/plan-1
git branch -D spike/005/plan-2
```

### SPIKE-SUMMARY.md Generation

Generate from WORKLOG files:

```markdown
# 005 Spike Summary: [Title]

## Executive Summary
**Recommendation: [Approach N]** - [One-line rationale]

## Questions Answered
### 1. [Question from SPIKE.md]
[Finding from WORKLOGs]

## Comparison
| Criteria | Approach 1 | Approach 2 | Winner |
|----------|------------|------------|--------|
| [metric] | [value]    | [value]    | [1/2]  |

## Recommendation Rationale
[Why this approach was chosen]

## Next Steps
- [ ] Create ADR documenting decision
- [ ] Create task for implementing chosen approach
```

### Follow-up Actions

**Create ADR:**
- Run `/adr` with spike findings as evidence
- Reference spike issue in ADR context

**Update Spec:**
- Add Technical Approach section to related SPEC.md
- Reference spike findings

**Create Tasks:**
- Run `/issue` to create implementation task
- Link to spike for context

---

## Error Handling

| Error | Resolution |
|-------|------------|
| Issue not found | Check `pm/issues/{ID}-*/` exists |
| Unknown type | Verify issue file exists (TASK.md/BUG.md/SPIKE.md) |
| Incomplete phases | List remaining phases, suggest `/implement {ID} --next` |
| Tests failing | Show failures, suggest fixing before completion |
| Spike branches missing | Check branch names, may need manual recovery |

## Integration

**Workflow position:**
```
/issue → /plan ### → /implement ### → /complete ### → /branch merge
```

**Related commands:**
- `/issue` - Create work items
- `/implement ###` - Execute plan phases
- `/branch merge` - Merge completed work to develop
