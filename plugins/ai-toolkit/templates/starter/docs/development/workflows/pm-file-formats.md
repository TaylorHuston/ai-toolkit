---
last_updated: "2025-11-18"
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
- Structure defined in templates/spec-template.md template
- Format: YAML frontmatter + Description + Acceptance Scenarios + Definition of Done + Tasks

**Tasks** (`pm/issues/###-name/TASK.md`):
- Implementation work for features or enhancements
- Created by `/issue` or `/spec` command
- Structure defined in templates/task-template.md template
- Format: YAML frontmatter + Description + Acceptance Criteria + Technical Notes

**Bugs** (`pm/issues/###-name/BUG.md`):
- Defect tracking and fixes
- Created by `/issue` or `/spec` command
- Structure defined in templates/bug-template.md template
- Format: YAML frontmatter + Description + Reproduction Steps + Environment

**Spikes** (`pm/issues/###-name/SPIKE.md`):
- Time-boxed exploration and research
- Created by `/issue` command
- Structure defined in templates/spike-template.md template
- Format: YAML frontmatter + Questions + Time Box + Approaches

**Plans** (`pm/issues/###-name/PLAN.md`):
- AI-managed implementation breakdown for tasks/bugs
- Created by `/plan` command
- Structure defined in templates/plan-template.md template
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
│   ├── ###-kebab-name/                 # Numeric ID (type from file)
│   │   ├── TASK.md                     # ← Type = task
│   │   ├── PLAN.md                     # How to build (AI-managed)
│   │   └── WORKLOG.md                  # What happened (AI-managed)
│   ├── ###-kebab-name/
│   │   ├── BUG.md                      # ← Type = bug
│   │   ├── PLAN.md                     # Fix approach
│   │   └── WORKLOG.md                  # Fix history
│   ├── ###-kebab-name/
│   │   ├── SPIKE.md                    # ← Type = spike
│   │   ├── PLAN-1.md                   # Exploration plan 1
│   │   ├── PLAN-2.md                   # Exploration plan 2
│   │   └── WORKLOG-1.md                # Per-plan worklogs
│   └── PROJ-###-kebab-name/            # External Jira issues
│       └── TASK.md                     # Jira issue local copy
└── templates/
    ├── spec.md                         # Spec template
    ├── task.md                         # Task template
    ├── bug.md                          # Bug template
    ├── spike.md                        # Spike template
    └── plan.md                         # Plan template
```

**Type Detection:** Commands detect issue type by which file exists (TASK.md, BUG.md, or SPIKE.md), not from the ID.

---

## Naming Conventions

**Kebab-case:** All file and directory names use lowercase-kebab-case
- Good: `user-authentication-system`, `fix-login-timeout`
- Bad: `UserAuthenticationSystem`, `fix_login_timeout`

**Numbering:**
- **Specs:** Sequential counter: SPEC-001, SPEC-002, SPEC-003
- **Issues:** Single numeric counter for ALL issue types: 001, 002, 003
  - Type determined by which file exists (TASK.md, BUG.md, SPIKE.md)
  - External issues (Jira): PROJ-123, PROJ-124 work seamlessly

Numbers are never reused, even if items are deleted. Gaps in sequence are normal.

**Number Assignment Algorithm:**
1. For specs: Scan `pm/specs/` for `SPEC-###-*` files
2. For issues: Scan `pm/issues/` for `###-*` directories
3. Find highest number
4. Increment by 1
5. Assign to new item

**Important:** Numbers continue sequence, they don't fill gaps.

**Example:**
```
Existing: SPEC-001, SPEC-002, SPEC-004 (SPEC-003 was deleted)
Next: SPEC-005 (continues sequence, doesn't fill gap)

Existing issues:
- 001-user-auth/TASK.md      (task linked to SPEC-001)
- 002-login-crash/BUG.md     (bug linked to SPEC-001)
- 003-graphql-vs-rest/SPIKE.md (standalone spike)

Creating new issue:
Next: 004 (continues global sequence)
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
- [ ] 001: User Registration
- [ ] 002: Login Flow
- [ ] 003: Password Reset
```

### Task File Format

```markdown
---
issue_number: 001
type: task
spec: SPEC-001       # Links task to spec (optional)
status: todo
created: 2025-01-15
jira_issue_key: null # Optional: Jira issue key if synced
---

# 001: User Registration

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
issue_number: 002
type: bug
spec: null          # Optional: parent spec if grouped
status: todo
severity: high
created: 2025-01-15
jira_issue_key: null
---

# 002: Session Timeout Not Working

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
issue_id: 001
plan_type: task
created: 2025-01-15
last_updated: 2025-01-15
complexity: 13
status: in_progress
---

# Implementation Plan: 001 User Registration

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
- `templates/spec-template.md` - Feature spec template with YAML config
- `templates/task-template.md` - Task template with YAML config
- `templates/bug-template.md` - Bug template with YAML config
- `templates/plan-template.md` - Plan template with YAML config
- `templates/README.md` - Template usage guide and custom types

**Workflow Guides:**
- `pm-workflows.md` - Spec creation, task creation, plan execution
- `task-workflow.md` - Implementation workflow and quality gates
- `worklog-format.md` - WORKLOG entry formats
- `git-workflow.md` - Branch naming aligned with issue IDs

**User Guides:**
- `pm/README.md` - Overview of PM directory for developers (in project templates)

**External Integration:**
- `jira-integration.md` - Jira mode, field discovery, promotion workflows
