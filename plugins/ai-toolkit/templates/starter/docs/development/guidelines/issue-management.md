---
# === Metadata ===
template_type: "guideline"
version: "1.0.0"
created: "2025-11-05"
last_updated: "2025-11-05"
status: "Active"
target_audience: ["AI Assistants", "Project Managers"]
description: "TASK.md, BUG.md, EPIC.md file structures and issue directory organization"

# === Configuration ===
issue_files: ["TASK.md", "BUG.md", "EPIC.md", "PLAN.md", "WORKLOG.md", "RESEARCH.md"]
epic_location: "pm/epics/"
issue_location: "pm/issues/"
---

# Issue Management - File Structures

**Purpose**: Define file formats for TASK.md, BUG.md, EPIC.md and directory organization

**Referenced by**: `/epic`, `/plan`, `/implement` commands

## Quick Reference

**File Types**:
- **EPIC.md** - Feature-level initiatives (`pm/epics/EPIC-###-name.md`)
- **TASK.md** - Implementation work (`pm/issues/TASK-###-name/TASK.md`)
- **BUG.md** - Defects and fixes (`pm/issues/BUG-###-name/BUG.md`)
- **PLAN.md** - Implementation phases (created by `/plan`)
- **WORKLOG.md** - Work history (created by `/implement`)
- **RESEARCH.md** - Technical decisions (optional)

**File Purposes**:
- **[TYPE].md** - WHAT to do (requirements, acceptance criteria)
- **PLAN.md** - HOW to do it (phase breakdown)
- **WORKLOG.md** - WHAT was done (narrative history)
- **RESEARCH.md** - WHY decisions were made (analysis)

---

## EPIC.md Format

**File Location**: `pm/epics/EPIC-###-<kebab-case-name>.md`

### Structure

```yaml
---
epic_number: EPIC-###
status: planning          # planning, in_progress, completed, on_hold
created: YYYY-MM-DD
updated: YYYY-MM-DD
owner: Team Name          # Optional
priority: high            # Optional: high, medium, low
target_date: YYYY-MM-DD   # Optional
---

# EPIC-###: Epic Name

## Description
[Epic goal and scope - what capability are we building?]
[Multi-paragraph explanation of the initiative]

## Definition of Done
[Concrete criteria that signal epic completion]
[Can be prose or checklist based on complexity]

**Prose format example:**
Users can create accounts, log in securely with email/password or Google OAuth,
reset forgotten passwords via email, and manage their profile information. The
system achieves 95%+ test coverage and passes security audit.

**Checklist format example:**
- [ ] Users can register with email/password
- [ ] Users can log in with Google OAuth
- [ ] Password reset via email works end-to-end
- [ ] Profile management (update email, password)
- [ ] 95%+ test coverage achieved
- [ ] Security audit passed with no critical findings

## Dependencies
- ADR-###: [Decision name]
- EPIC-###: [Other epic dependency]
- External: [External dependencies]

## Tasks
- [ ] TASK-001: User Registration
- [ ] TASK-002: Login Flow
- [ ] BUG-003: Session Timeout Fix
- [ ] TASK-004: Password Reset

## Scope (Optional)
**In Scope:**
- Feature X, Y, Z

**Out of Scope:**
- Feature A, B (deferred to EPIC-###)

## Technical Notes (Optional)
[Implementation considerations, technology choices, architectural notes]
```

### Naming Conventions

**Pattern**: `EPIC-###-<kebab-case-name>.md`

**Examples**:
- `EPIC-001-user-authentication.md`
- `EPIC-002-payment-processing.md`
- `EPIC-003-admin-dashboard.md`

**Rules**:
- Sequential numbering starting from 001
- Kebab-case for multi-word names
- Descriptive but concise (3-5 words ideal)
- Focus on capability, not implementation

### Epic Size Guidelines

**Recommended**: 2-8 tasks
**Minimum**: 1 task (acceptable for significant features)
**Maximum**: 10 tasks (consider decomposition above this)

**Why decompose large epics:**
- More than 10 tasks
- Tasks span multiple domains with no shared context
- Epic takes more than 3 sprints/iterations
- Definition of Done is vague or overly broad

**Decomposition example**:
```
Original: EPIC-001 "E-commerce Platform" (25 tasks)
  ↓
Decomposed:
- EPIC-001: "Product Catalog" (5 tasks)
- EPIC-002: "Shopping Cart" (4 tasks)
- EPIC-003: "Checkout Flow" (6 tasks)
- EPIC-004: "Order Management" (5 tasks)
- EPIC-005: "Admin Panel" (5 tasks)
```

---

## TASK.md Format

**File Location**: `pm/issues/TASK-###-<kebab-case-name>/TASK.md`

### Structure

```yaml
---
issue_number: TASK-###
type: task
epic: EPIC-###            # Optional: which epic this belongs to
status: todo              # todo, in_progress, completed, blocked
created: YYYY-MM-DD
updated: YYYY-MM-DD
assignee: Person Name     # Optional
priority: medium          # Optional: high, medium, low
estimate: 5               # Optional: story points or hours
---

# TASK-###: Task Name

## Description
[What needs to be implemented - clear, concise explanation]
[Context about why this work is needed]
[Any relevant background information]

## Acceptance Criteria
[What must be true for this task to be considered complete]

- [ ] Criterion 1: User can [action] with [condition]
- [ ] Criterion 2: System [behavior] when [condition]
- [ ] Criterion 3: Tests pass with 95%+ coverage
- [ ] Criterion 4: Documentation updated

**Best Practice**: Use checkboxes for concrete, testable criteria

## Technical Notes (Optional)
[Implementation considerations, technology choices, edge cases to handle]
[Links to relevant ADRs or documentation]
```

### Naming Conventions

**Pattern**: `TASK-###-<kebab-case-name>/`

**Examples**:
- `TASK-001-user-registration/`
- `TASK-002-login-flow/`
- `TASK-003-database-schema/`

**Rules**:
- Global sequential numbering (TASK-001, TASK-002, across entire project)
- Kebab-case for multi-word names
- Descriptive (appears in file paths and git branches)
- Not per-epic (continues global sequence)

---

## BUG.md Format

**File Location**: `pm/issues/BUG-###-<kebab-case-name>/BUG.md`

### Structure

```yaml
---
issue_number: BUG-###
type: bug
epic: EPIC-###            # Optional: if part of epic
status: todo              # todo, in_progress, completed, blocked
severity: medium          # critical, high, medium, low
created: YYYY-MM-DD
updated: YYYY-MM-DD
assignee: Person Name     # Optional
---

# BUG-###: Bug Title

## Description
[Clear description of the bug - what's broken?]
[When was it discovered?]
[Impact on users or system]

## Steps to Reproduce
1. Step one
2. Step two
3. Step three

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [Linux/macOS/Windows]
- Browser: [Chrome 120 / Safari 17 / etc.]
- Version: [App version]
- Other relevant details

## Acceptance Criteria
[What must be true for this bug to be considered fixed]

- [ ] Bug no longer reproduces following steps above
- [ ] Regression test added to prevent recurrence
- [ ] Related edge cases tested
- [ ] Fix verified in all affected environments

## Technical Notes (Optional)
[Root cause analysis, potential solutions, affected components]
```

### Naming Conventions

**Pattern**: `BUG-###-<kebab-case-name>/`

**Examples**:
- `BUG-001-session-timeout/`
- `BUG-002-login-redirect-loop/`
- `BUG-003-payment-validation-error/`

**Rules**:
- Global sequential numbering (BUG-001, BUG-002, across entire project)
- Kebab-case for multi-word names
- Descriptive of the problem (not the solution)

---

## Issue Directory Structure

### Complete Directory Layout

```
pm/issues/TASK-001-user-registration/
├── TASK.md        # Definition, acceptance criteria (REQUIRED)
├── PLAN.md        # Implementation phases (created by /plan)
├── WORKLOG.md     # Work history (created by /implement)
└── RESEARCH.md    # Technical deep dives (optional, created as needed)
```

### File Lifecycle

**1. Epic/Issue Creation** (`/epic` or manual)
```
Creates: pm/issues/TASK-###-name/TASK.md
Contains: Description, acceptance criteria
Status: Ready for planning
```

**2. Planning** (`/plan TASK-###`)
```
Creates: pm/issues/TASK-###-name/PLAN.md
Contains: Phase breakdown, approach, review notes
Status: Ready for implementation
```

**3. Implementation** (`/implement TASK-### 1.1`)
```
Creates: pm/issues/TASK-###-name/WORKLOG.md (first time)
Updates: WORKLOG.md (subsequent phases)
Contains: Work narrative, lessons, handoffs
Status: In progress
```

**4. Complex Decisions** (manual or agent-created)
```
Creates: pm/issues/TASK-###-name/RESEARCH.md
Contains: Technical analysis, alternatives, benchmarks
Status: Referenced from WORKLOG entries
```

### File Relationships

```
TASK.md         →  Defines WHAT to build
    ↓
PLAN.md         →  Defines HOW to build it (phases)
    ↓
WORKLOG.md      →  Documents WHAT was done (history)
    ↓
RESEARCH.md     →  Explains WHY decisions were made
```

---

## Issue Type Detection

### Automatic Type Detection

**Bug indicators** (use BUG-### prefix):
- Keywords: "bug", "fix", "broken", "regression", "error", "crash"
- Context: "not working", "fails", "incorrect behavior"

**Task indicators** (use TASK-### prefix):
- Keywords: "feature", "add", "implement", "create", "build"
- Context: "should", "want", "need to"

**Custom types** (if configured):
- SPIKE-###: "research", "investigate", "explore", "proof of concept"
- RFC-###: "proposal", "design", "RFC", "architecture decision"

### Custom Issue Types

**Configuration location**: `pm-guidelines.md` machine-readable config

**Adding custom types**:
1. Create template: `pm/templates/[type].md`
2. Update pm-guidelines.md configuration
3. Use pattern: `[TYPE]-###-name/[TYPE].md`

**Common custom types**:
- **SPIKE** - Research, investigation, POC
- **RFC** - Design proposals, architecture decisions
- **EXPERIMENT** - Time-boxed experiments
- **DEBT** - Technical debt remediation

---

## Jira Integration

**Mode detection**: Commands read CLAUDE.md for Jira configuration

### Local Mode (Jira Disabled - Default)

**Epics**: `pm/epics/EPIC-###-name.md` files
**Issues**: `pm/issues/TASK-###/`, `BUG-###/` directories
**Benefits**: Fully offline, no external dependencies

### Jira Mode (Jira Enabled)

**Epics**: Created in Jira ONLY (PROJ-100, PROJ-200)
- No local epic files
- `pm/epics/` remains empty

**Issues**: Hybrid approach
- Jira issues: `PROJ-###` (fetched on-demand)
- Local exploration: `TASK-###`, `BUG-###` (can be promoted to Jira)

**Local storage for Jira issues**:
```
pm/issues/PROJ-123-feature-name/
├── PLAN.md        # AI-managed implementation phases
├── WORKLOG.md     # Work history
└── RESEARCH.md    # Technical decisions
```

**Note**: No TASK.md file for Jira issues (Jira is source of truth)

### Promotion Pattern

**Local → Jira migration**:
```bash
# After local exploration validates approach
/promote TASK-001

# Creates PROJ-124 in Jira
# Copies acceptance criteria
# Updates epic linkage
# Preserves PLAN.md, WORKLOG.md, RESEARCH.md
```

---

## Related Documentation

**For planning phases**:
- See `plan-structure.md` for PLAN.md format
- See `plan-structure.md` for phase patterns and review requirements

**For work documentation**:
- See `worklog-format.md` for WORKLOG.md entry formats
- See `research-documentation.md` for RESEARCH.md format

**For management patterns**:
- See `pm-guidelines.md` for epic/issue creation workflows
- See `pm-guidelines.md` for Jira integration details

**For workflow**:
- See `development-loop.md` for implementation workflow
- See `development-loop.md` for acceptance criteria validation
