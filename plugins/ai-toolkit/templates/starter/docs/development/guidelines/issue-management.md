---
# === Metadata ===
template_type: "guideline"
created: "2025-11-05"
last_updated: "2025-11-06"
status: "Active"
target_audience: ["AI Assistants", "Project Managers"]
description: "TASK.md, BUG.md, EPIC.md file structures and issue directory organization"

# === Configuration ===
issue_files: ["TASK.md", "BUG.md", "EPIC.md", "PLAN.md", "WORKLOG.md"]
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

**File Purposes**:
- **[TYPE].md** - WHAT to do (requirements, acceptance criteria) - **Living document**
- **PLAN.md** - HOW to do it (phase breakdown) - **Living document**
- **WORKLOG.md** - WHAT was done and WHY (narrative history with rationale) - **Immutable history**
- **Architecture decisions** - Use `/adr` command for complex technical decisions

**Living Documents Model**:
- TASK.md and PLAN.md evolve based on implementation learnings
- Updated after phase reviews (code review, security audit, architecture review)
- Files reflect current understanding, not historical debates
- WORKLOG.md documents why changes were made (audit trail)

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
└── WORKLOG.md     # Work history (created by /implement)
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
Contains: Work narrative, lessons, handoffs, decisions and rationale
Status: In progress
```

**4. Architecture Decisions** (when needed)
```
Use: /adr command
Creates: docs/adr/ADR-###-title.md (project-wide)
Contains: Context, decision, alternatives, consequences
Status: Referenced from WORKLOG or code comments
```

### File Relationships

```
TASK.md         →  Defines WHAT to build
    ↓
PLAN.md         →  Defines HOW to build it (phases)
    ↓
WORKLOG.md      →  Documents WHAT was done and WHY (history with rationale)
    ↓
ADRs (via /adr) →  Complex architecture decisions (project-wide)
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
└── WORKLOG.md     # Work history
```

**Note**: No TASK.md file for Jira issues (Jira is source of truth). Use `/adr` for complex architecture decisions.

### Promotion Pattern

**Local → Jira migration**:
```bash
# After local exploration validates approach
/promote TASK-001

# Creates PROJ-124 in Jira
# Copies acceptance criteria
# Updates epic linkage
# Preserves PLAN.md, WORKLOG.md
```

---

## File Evolution and Size Guidelines

### File Lifecycle

**TASK.md / BUG.md**:
- Created: At task/bug creation
- Updated: When requirements legitimately discovered during implementation
- Completed: Status changes to complete, file preserved for history
- Size: 50-200 lines (grows as requirements discovered)

**PLAN.md**:
- Created: By `/plan` command after TASK/BUG exists
- Updated: After each phase review based on learnings (agile adaptation)
- Completed: When all phases done, no more updates
- Size: 300-600 lines (evolves with learnings, includes new phases from reviews)

**WORKLOG.md**:
- Created: First `/implement` execution
- Updated: Continuously during implementation, after each review cycle
- Completed: When task completes (frozen as historical record)
- Size: 400-800 lines for complex tasks (includes review cycles and adaptations)

### Size Management

**Agile workflow produces larger but manageable files**:
- Reviews discover new requirements → TASK.md grows
- Learnings adapt phases → PLAN.md evolves
- Review cycles documented → WORKLOG.md captures decisions

**Target total**: ~1,200 lines for well-documented complex task

**What causes bloat** (avoid these):
- ❌ Preserving debate history inline in PLAN.md
- ❌ Pasting full review reports into files
- ❌ Verbose troubleshooting logs in WORKLOG.md
- ❌ Creating extra files (RESEARCH.md, ANALYSIS.md, etc.)

**Keep files clean**:
- ✅ Update TASK/PLAN with current state only
- ✅ Summarize review findings in WORKLOG (link to detailed reports)
- ✅ Remove obsolete approaches as you learn
- ✅ Use `/adr` for architecture decisions needing detailed documentation

See `plan-structure.md` "Agile Plan Updates" for update patterns and examples.

---

## Related Documentation

**For planning phases**:
- See `plan-structure.md` for PLAN.md format
- See `plan-structure.md` for phase patterns and review requirements
- See `plan-structure.md` for agile plan updates guidance

**For work documentation**:
- See `worklog-format.md` for WORKLOG.md entry formats
- See `worklog-format.md` for PLAN CHANGES entry format
- Use `/adr` command for architecture decisions requiring detailed documentation

**For management patterns**:
- See `pm-guidelines.md` for epic/issue creation workflows
- See `pm-guidelines.md` for Jira integration details

**For workflow**:
- See `development-loop.md` for implementation workflow
- See `development-loop.md` for review and adapt cycle
- See `development-loop.md` for acceptance criteria validation
