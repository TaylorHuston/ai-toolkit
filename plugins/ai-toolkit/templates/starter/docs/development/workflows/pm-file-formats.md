---
# === Metadata ===
template_type: "guideline"
created: "2025-11-18"
last_updated: "2025-11-18"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "PM file structure, naming conventions, and directory organization for local project management"
---

# PM File Formats & Structure

**Referenced by Commands:** `/spec`, `/plan`, `/implement`, `/advise`, `/toolkit-init`

## Overview

This guide defines file structures, naming conventions, and directory organization for local project management. It is **Jira-agnostic** and works entirely with local files.

For workflow details (spec creation, task creation, plan execution), see **pm-workflows.md**.

---

## File Types

**Specs** (`pm/specs/SPEC-###-name.md`):
- Feature-level initiatives containing multiple tasks
- Created by `/spec` command
- Structure defined in templates/spec.md template
- Format: YAML frontmatter + Description + Acceptance Scenarios + Definition of Done + Tasks

**Tasks** (`pm/issues/TASK-###-name/TASK.md`):
- Implementation work for features or enhancements
- Created by `/spec` command or standalone
- Structure defined in templates/task.md template
- Format: YAML frontmatter + Description + Acceptance Criteria + Technical Notes

**Bugs** (`pm/issues/BUG-###-name/BUG.md`):
- Defect tracking and fixes
- Created by `/spec` command or standalone
- Structure defined in templates/bug.md template
- Format: YAML frontmatter + Description + Reproduction Steps + Environment

**Plans** (`pm/issues/TASK-###-name/PLAN.md`):
- AI-managed implementation breakdown for tasks/bugs
- Created by `/plan` command
- Structure defined in templates/plan.md template
- Format: YAML frontmatter + Overview + Phases + Scenario Coverage + Complexity Analysis
- Stays separate from TASK.md/BUG.md (which may sync with external PM tools)

**Templates are Source of Truth:** See templates/ directory for complete file structure definitions.

---

## Directory Structure

```
pm/
├── specs/
│   └── SPEC-###-kebab-name.md          # Feature specs
├── issues/
│   ├── TASK-###-kebab-name/
│   │   ├── TASK.md                     # What to build
│   │   ├── PLAN.md                     # How to build (AI-managed)
│   │   └── WORKLOG.md                  # What happened (AI-managed)
│   └── BUG-###-kebab-name/
│       ├── BUG.md                      # Bug description
│       ├── PLAN.md                     # Fix approach
│       └── WORKLOG.md                  # Fix history
└── templates/
    ├── spec.md                         # Spec template
    ├── task.md                         # Task template
    ├── bug.md                          # Bug template
    └── plan.md                         # Plan template
```

---

## Naming Conventions

**Kebab-case:** All file and directory names use lowercase-kebab-case
- Good: `user-authentication-system`, `fix-login-timeout`
- Bad: `UserAuthenticationSystem`, `fix_login_timeout`

**Numbering:** Sequential global per type
- SPEC-001, SPEC-002, SPEC-003 (feature specs)
- TASK-001, TASK-002, TASK-003 (tasks, across all specs)
- BUG-001, BUG-002, BUG-003 (bugs, across all specs)

Numbers are never reused, even if items are deleted. Gaps in sequence are normal.

**Number Assignment Algorithm (per issue type):**
1. Scan `pm/specs/` or `pm/issues/` directory
2. Parse all `[TYPE]-###-*` filenames/directories
3. Find highest number (e.g., TASK-004 or SPEC-003)
4. Increment by 1 (next: TASK-005 or SPEC-004)
5. Assign to new item

**Important:** Numbers continue sequence, they don't fill gaps.

**Example:**
```
Existing: SPEC-001, SPEC-002, SPEC-004 (SPEC-003 was deleted)
Next: SPEC-005 (continues sequence, doesn't fill gap)

Existing issues:
- TASK-001 (spec: SPEC-001)
- TASK-002 (spec: SPEC-001)
- TASK-003 (spec: SPEC-002)

Creating task for SPEC-003:
Next: TASK-004 (continues global sequence, not per-spec)
```

---

## File Content Formats

### Spec File Format

```markdown
---
spec_number: SPEC-001
title: "User Authentication System"
status: planning
created: 2025-01-15
jira_epic_key: null  # Optional: Jira epic key if synced
---

# SPEC-001: User Authentication System

## Description
[Feature description and business value]

## Acceptance Scenarios
1. **Scenario: User logs in successfully**
   - Given: User has valid credentials
   - When: User submits login form
   - Then: User is redirected to dashboard
   - And: Session cookie is set

[... more scenarios ...]

## Definition of Done
- [ ] All acceptance scenarios validated
- [ ] Test coverage ≥80%
- [ ] Security audit passed
- [ ] Documentation complete

## Tasks
- [ ] TASK-001: User Registration
- [ ] TASK-002: Login Flow
- [ ] TASK-003: Password Reset
```

### Task File Format

```markdown
---
issue_number: TASK-001
type: task
spec: SPEC-001       # Links task to spec (optional)
status: todo
created: 2025-01-15
jira_issue_key: null # Optional: Jira issue key if synced
---

# TASK-001: User Registration

## Description
[Detailed task description]

## Acceptance Criteria
- [ ] User can register with email/password
- [ ] Password strength validation enforced
- [ ] Email verification sent

## Technical Notes
[Implementation hints, constraints, dependencies]
```

### Bug File Format

```markdown
---
issue_number: BUG-001
type: bug
spec: null          # Optional: parent spec if grouped
status: todo
severity: high
created: 2025-01-15
jira_issue_key: null
---

# BUG-001: Session Timeout Not Working

## Description
[Bug description]

## Reproduction Steps
1. Login to application
2. Wait 30 minutes
3. Attempt to access protected page
4. Expected: Redirected to login
5. Actual: Page loads with stale session

## Environment
- Browser: Chrome 120
- OS: macOS 14
- Server: Node 18.x
```

### Plan File Format

```markdown
---
issue_id: TASK-001
plan_type: task
created: 2025-01-15
last_updated: 2025-01-15
complexity: 13
status: in_progress
---

# Implementation Plan: TASK-001

## Overview
[Brief summary of implementation approach]

## Phases

### Phase 1: Authentication Backend
- [ ] 1.1 Design JWT token structure
- [ ] 1.2 Implement user model with bcrypt
- [ ] 1.3 Create login endpoint (/api/auth/login)
- [ ] 1.4 Add JWT generation and validation
- [ ] 1.5 Write authentication integration tests

[... more phases ...]

## Scenario Coverage

✓ SPEC-001 Scenario 1: "User logs in successfully"
  → Covered by Phase 1.5 (integration tests validate successful login flow)

[... more scenarios ...]

## Complexity Analysis
**Score: 13 points (Medium)**
- File count: 5-8 files
- Dependencies: 2 new (bcrypt, jsonwebtoken)
- Test complexity: Integration tests required
- Security: Critical - auth implementation
```

---

## Status Management

### Spec Status Values

- `planning` - Spec defined, tasks not yet created or planned
- `in_progress` - At least one task started
- `completed` - All tasks complete, Definition of Done satisfied
- `on_hold` - Blocked or deprioritized

**Updates:** Manual in spec frontmatter, or inferred from task status.

### Task/Bug Status Values

- `todo` - Created, not started
- `in_progress` - Implementation underway
- `completed` - All acceptance criteria met
- `blocked` - Waiting on dependency or decision

**Updates:** `/implement` updates automatically, or manual in frontmatter.

---

## External Integration

**Jira Mode:** For projects using Jira, see `jira-integration.md` for:
- Dual-mode behavior (local + Jira)
- Field discovery and mapping
- Promotion workflows (local → Jira)
- Import workflows (Jira → local)

---

## Related Documentation

**Templates (Source of Truth for File Structure):**
- `templates/spec.md` - Feature spec template with YAML config
- `templates/task.md` - Task template with YAML config
- `templates/bug.md` - Bug template with YAML config
- `templates/plan.md` - Plan template with YAML config
- `templates/README.md` - Template usage guide and custom types

**Workflow Guides:**
- `pm-workflows.md` - Spec creation, task creation, plan execution
- `development-loop.md` - Implementation workflow and quality gates
- `worklog-format.md` - WORKLOG entry formats
- `git-workflow.md` - Branch naming aligned with issue IDs

**User Guides:**
- `pm/README.md` - Overview of PM directory for developers (in project templates)

**External Integration:**
- `jira-integration.md` - Jira mode, field discovery, promotion workflows
