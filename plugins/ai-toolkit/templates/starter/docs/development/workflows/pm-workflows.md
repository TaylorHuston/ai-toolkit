---
last_updated: "2025-11-26"
description: "PM workflows for spec creation, task planning, and implementation execution"
---

# PM Workflows

**Referenced by:** `/spec`, `/spike`, `/plan`, `/implement`, `/advise`

## File Purposes

| File | Purpose | Contains | Created By |
|------|---------|----------|------------|
| SPEC.md | WHAT feature (user perspective) | Description, BDD scenarios, definition of done | `/spec`, `/jira-epic` |
| TASK.md | WHAT work item (PM perspective) | Description, acceptance criteria, tech notes | `/spec` task creation |
| SPIKE.md | WHAT questions to answer | Questions, time box, approaches | `/spike` |
| PLAN.md | HOW to implement | Phases, steps, scenario coverage | `/plan` |

**Key distinctions:**
- SPEC = Feature-level (multi-task), BDD scenarios
- TASK = Work-item-level (single deployable change), acceptance criteria
- SPIKE = Exploration (produces findings), time-boxed
- PLAN = Implementation breakdown (evolves during work)

**Syncing:**
- SPEC.md, TASK.md sync with Jira (if enabled)
- SPIKE.md, PLAN.md are purely local (never sync)

**See:** `pm-file-formats.md` for directory structure and naming conventions.

---

## Content Decision Matrix

| Content Type | SPEC | TASK | SPIKE | PLAN |
|-------------|------|------|-------|------|
| Feature description (user perspective) | ✅ | | | |
| Acceptance scenarios (BDD) | ✅ | | | |
| Work item description | | ✅ | | |
| Acceptance criteria (checklist) | | ✅ | | |
| Technical constraints/notes | | ✅ | | |
| Questions to answer | | | ✅ | |
| Time box and deadline | | | ✅ | |
| Findings and recommendation | | | ✅ | |
| Phase breakdown | | | | ✅ |
| Implementation steps | | | | ✅ |
| Scenario coverage mapping | | | | ✅ |

---

## Spec Creation Workflow

**Command:** `/spec`

### When to Create Specs

- **Features**: Multi-task initiatives (2-8 tasks per spec)
- **Epics**: Large bodies of work requiring phased delivery
- **Above 10 tasks**: Consider decomposing into multiple specs

### Conversational Gathering

1. **Spec Name** - Capability focus, not implementation ("User Authentication" not "Add Login API")
2. **Description** - User perspective, business value
3. **Acceptance Scenarios** - 2-5 key scenarios in Given/When/Then BDD format
4. **Definition of Done** - Concrete, measurable outcomes
5. **Dependencies** - ADRs, other specs (optional)

### Task Suggestion

After spec creation, `/spec` optionally suggests 2-3 foundational tasks. User can accept all, select specific, describe custom, or skip.

---

## Task/Bug Creation Workflow

### Scoping Principles

**Task Scope = Deployable Change:**
- Merged to main as complete unit
- Independently deployable
- Typical: 1-3 days, 3-8 phases

**Phase Scope = Logical Commit Point:**
- Committed as logical unit
- Testable, reviewable
- Typical: 1-4 hours

**Key question:** If task feels too small to deploy, it's probably a phase within a larger task.

### Task vs Bug

| Type | Keywords | Purpose |
|------|----------|---------|
| TASK | add, implement, create, build | New features, enhancements |
| BUG | fix, broken, regression | Defects, broken functionality |

### Spec Linkage

**Task → Spec:** `spec: SPEC-001` in YAML frontmatter
**Spec → Task:** Added to Tasks section in SPEC.md

---

## Plan Creation Workflow

**Command:** `/plan ###`

### Process

1. **Load Context** - Read TASK/BUG, parent SPEC (if referenced), extract acceptance scenarios
2. **Research** - Library docs, best practices, security considerations (if needed)
3. **Architectural Review** - code-architect validates design
4. **Security Review** - security-auditor reviews (if security-relevant)
5. **Generate Phases** - Break into TDD phases with scenario coverage
6. **Present Plan** - Display phases, coverage mapping, complexity estimate

### Phase Structure

**Choose based on task type:**

| Task Type | Structure | TDD? |
|-----------|-----------|------|
| Feature with testable behavior | TDD Phases (X.RED/X.GREEN/X.REFACTOR) | YES |
| Bug fix with reproducible failure | TDD Phases | YES |
| Infrastructure/scaffolding | Simple Phases | NO |
| Documentation-only | Simple Phases | NO |

**TDD Phase Structure:**
```
Phase X - {Behavior}
├── X.RED - Write failing tests (MUST fail before proceeding)
├── X.GREEN - Implement to pass (minimal code)
└── X.REFACTOR - Clean up (loops until review ≥90)
```

**See:** `task-workflow.md` for complete TDD loop details and checkpoints.

### Scenario Coverage

When TASK references parent SPEC with acceptance scenarios:

```markdown
### SPEC-001 Scenario 1: User logs in successfully
- **Given/When/Then**: User submits valid credentials → redirected to dashboard
- **Coverage**: Phase 1.5 validates via integration tests
- **Test Strategy**: Tests for successful login, token generation, session cookie
```

**See:** `templates/plan-template.md` for complete coverage format.

### Complexity Scoring

| Level | Points | Indicators |
|-------|--------|------------|
| Low | 1-3 | Single file, clear requirements, no external deps |
| Medium | 4-6 | Multi-file, some ambiguity, library integration |
| High | 7-10 | Architectural changes, multiple integrations, security-critical |

**Decomposition threshold:** Tasks >8 points should be split.

**See:** `quality-gates.md` for detailed scoring criteria.

---

## Plan Execution Workflow

**Command:** `/implement ### PHASE`

### TDD Execution Flow

```
For each TDD phase:

X.RED - Write Failing Tests:
  1. Write tests for behavior
  2. Run tests → MUST FAIL
  3. If PASS → BLOCK (not testing new behavior)

X.GREEN - Implement to Pass:
  4. Write minimal code to pass
  5. Run tests → MUST PASS

X.REFACTOR - Clean Up:
  6. Refactor for maintainability
  7. Run tests → must still pass
  8. Code review → if <90, repeat 6-8
  9. Review ≥90 → commit, next phase
```

**See:** `task-workflow.md` for complete loop details, checkpoints, and quality gates.

### Phase Completion Checklist

All required before proceeding:
- ✅ Tests written and initially failing (RED)
- ✅ Tests passing after implementation (GREEN)
- ✅ Code review ≥90
- ✅ Changes committed
- ✅ WORKLOG Phase Commits updated (e.g., `- Phase 1.1: abc123d - Description`)
- ✅ WORKLOG entry added
- ✅ PLAN.md checkboxes updated

### Plan Adaptation

Plans are living documents. Update when:
- Code reviews reveal better approaches
- Complexity higher/lower than estimated
- Previous phase discoveries affect future phases

**Protocol:**
1. Read WORKLOG.md for context
2. Update PLAN.md phases
3. Add WORKLOG entry explaining changes
4. Continue with updated plan

---

## Test Description Quality

**Strategic with tactical specificity:**

✅ **Good:**
- "Test workspace scoping (ADRs from workspace A not visible in workspace B's getAll)"
- "Test CASCADE DELETE (workspace deletion removes child ADRs)"
- "Test invalid workspaceId (Prisma P2003 foreign key error)"

❌ **Too Vague:**
- "Test workspace scoping"
- "Test error handling"

❌ **Too Prescriptive:**
- "Use beforeEach to create two workspaces, insert ADRs, assert getAll filters correctly"

**Implementation hints (include):** API facts, gotchas, default behaviors
**Implementation prescriptions (avoid):** HOW to code it

---

## Common Patterns

### Phased Delivery Spec
```
SPEC-001: "Payment Processing"
├── Phase 1 (MVP): 001 (task), 002 (task), 003 (task)
└── Phase 2 (Enhancements): 004 (task), 005 (task), 006 (task)
```

### Bug Fix Spec
```
SPEC-002: "Session Management Improvements"
├── 007 (bug): Session Timeout Not Working
├── 008 (bug): Session Cookies Not Secure
└── 009 (task): Add Session Monitoring
```

### Spike Before Implementation
```
SPEC-003: "Real-time Notifications"
├── 010 (spike): Research WebSocket vs SSE
└── 011 (task): Implement chosen solution
```

---

## Related Files

- `pm-file-formats.md` - Directory structure, naming conventions
- `task-workflow.md` - TDD loop, quality gates, agent orchestration
- `quality-gates.md` - Scoring criteria, gate hierarchy
- `spike-workflow.md` - Time-boxed exploration workflow
- `worklog-format.md` - WORKLOG entry formats
- `templates/` - SPEC, TASK, BUG, PLAN templates
