---
# === Metadata ===
template_type: "guideline"
created: "2025-11-13"
last_updated: "2025-11-13"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "Core PM workflows and plan execution methodology for local project management"
---

# PM Guide - Core Workflows & Execution

**Referenced by Commands:** `/spec`, `/plan`, `/implement`, `/advise`

## Overview

This guide defines core project management workflows for creating and executing work. It is **Jira-agnostic** and works entirely with local files.

### File Types

**Specs** (`pm/specs/SPEC-###-name.md`):
- Feature-level initiatives containing multiple tasks
- Created by `/spec` command
- Structure defined in `pm/templates/spec.md` template
- Format: YAML frontmatter + Description + Acceptance Scenarios + Definition of Done + Tasks

**Tasks** (`pm/issues/TASK-###-name/TASK.md`):
- Implementation work for features or enhancements
- Created by `/spec` command or standalone
- Structure defined in `pm/templates/task.md` template
- Format: YAML frontmatter + Description + Acceptance Criteria + Technical Notes

**Bugs** (`pm/issues/BUG-###-name/BUG.md`):
- Defect tracking and fixes
- Created by `/spec` command or standalone
- Structure defined in `pm/templates/bug.md` template
- Format: YAML frontmatter + Description + Reproduction Steps + Environment

**Plans** (`pm/issues/TASK-###-name/PLAN.md`):
- AI-managed implementation breakdown for tasks/bugs
- Created by `/plan` command
- Structure defined in `pm/templates/plan.md` template
- Format: YAML frontmatter + Overview + Phases + Scenario Coverage + Complexity Analysis
- Stays separate from TASK.md/BUG.md (which may sync with external PM tools)

**Templates are Source of Truth:** See `pm/templates/*.md` for complete file structure definitions.

### Directory Structure

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

### Naming Conventions

**Kebab-case:** All file and directory names use lowercase-kebab-case
- Good: `user-authentication-system`, `fix-login-timeout`
- Bad: `UserAuthenticationSystem`, `fix_login_timeout`

**Numbering:** Sequential global per type
- SPEC-001, SPEC-002, SPEC-003 (feature specs)
- TASK-001, TASK-002, TASK-003 (tasks, across all specs)
- BUG-001, BUG-002, BUG-003 (bugs, across all specs)

Numbers are never reused, even if items are deleted. Gaps in sequence are normal.

### External Integration

**Jira Mode:** For projects using Jira, see `jira-integration.md` for dual-mode behavior and promotion workflows.

---

## Spec Creation Workflow

**Pattern Used by `/spec` Command**

### When to Create Specs

Create feature specs for:
- **Features**: Multi-task initiatives (authentication, payment processing, admin dashboard)
- **Epics**: Large bodies of work requiring phased delivery
- **Related Work**: Grouping 2-8 related tasks under one feature umbrella

**Recommended Size:** 2-8 tasks per spec (sweet spot for focused delivery)
- **Below 2 tasks:** Consider if a spec is needed (single-task specs are okay for significant features)
- **Above 10 tasks:** Consider decomposing into multiple specs

### Conversational Creation Pattern

The `/spec` command uses natural conversation to gather:

1. **Spec Name** (extracted from conversation or asked)
   - Focus on capability, not implementation
   - Good: "User Authentication", "Payment Processing", "Admin Dashboard"
   - Avoid: "Add Login API Endpoints", "Build React Components"

2. **Description** (what capability are we building)
   - Clear explanation of the feature
   - User perspective and business value

3. **Acceptance Scenarios** (2-5 key scenarios in Given-When-Then format)
   - Happy paths and critical edge cases
   - BDD format: Given/When/Then/And
   - These scenarios become test phases in `/plan`
   - Example:
     ```
     Scenario: User logs in successfully
     - Given: User has valid credentials
     - When: User submits login form
     - Then: User is redirected to dashboard
     - And: Session cookie is set
     ```

4. **Definition of Done** (completion criteria)
   - Concrete, measurable outcomes
   - Technical and business criteria
   - Quality standards (test coverage, performance, security)

5. **Dependencies** (optional)
   - ADRs (Architecture Decision Records)
   - Other specs
   - External factors

**Philosophy:** Natural on surface, structured underneath. Commands handle structure; users describe intent.

### Number Assignment Logic

**Algorithm:**
1. Scan `pm/specs/` directory
2. Parse all `SPEC-###-*` filenames
3. Find highest number (e.g., SPEC-004)
4. Increment by 1 (next: SPEC-005)
5. Assign to new spec

**Important:** Numbers continue sequence, they don't fill gaps.

**Example:**
```
Existing: SPEC-001, SPEC-002, SPEC-004 (SPEC-003 was deleted)
Next: SPEC-005 (continues sequence)
```

### Task Suggestion Strategy

After spec creation, `/spec` optionally suggests initial tasks:

**Analysis Approach:**
1. Parse spec description for key features
2. Identify foundational components (database, auth, core logic)
3. Suggest 2-3 foundational tasks
4. User selects, customizes, or describes own

**Example:**
```
Spec: "User Authentication System"
Description: "Login, registration, password reset, Google OAuth"

Suggested Tasks:
1. TASK-###: User Registration (core capability)
2. TASK-###: Login Flow (core capability)
3. TASK-###: Database Schema for Users (foundational)

User can:
- Accept all
- Select specific ones
- Describe custom task
- Skip task creation
```

**Interactive Loop:**
```
AI: "Create these tasks? (yes/all/custom/stop)"
User: "custom"
AI: "Describe the task you want to create..."
User: "Add password strength validation"
AI: "Creating TASK-###..."
AI: "Create another? (yes/no)"
```

---

## Task/Bug Creation Workflow

**Pattern Used by `/spec` During Task Creation**

### When to Create Tasks vs Bugs

**Tasks:**
- New features or enhancements
- Implementation work
- Keywords: "add", "implement", "create", "build"

**Bugs:**
- Defects, regressions, broken functionality
- Keywords: "fix", "bug", "broken", "regression"

**Detection:** Commands infer type from conversation, but users can specify explicitly.

### Number Assignment Logic

**Algorithm (per issue type):**
1. Scan `pm/issues/` for all `[TYPE]-###-*` directories
2. Find highest number for this type
3. Increment by 1
4. Assign to new issue

**Important:** Numbering is GLOBAL per type, not per-spec.

**Example:**
```
Existing issues:
- TASK-001 (spec: SPEC-001)
- TASK-002 (spec: SPEC-001)
- TASK-003 (spec: SPEC-002)

Creating task for SPEC-003:
Next: TASK-004 (continues global sequence)
```

### Template-Driven Creation

**Process:**
1. Determine issue type (TASK, BUG, etc.)
2. Read corresponding template (`pm/templates/[type].md`)
3. Parse template YAML frontmatter for required sections
4. Gather details conversationally
5. Create `pm/issues/[TYPE]-###-name/[TYPE].md` following template

**Template sections are automatically parsed from YAML frontmatter.**

### Spec Linkage (Bidirectional References)

**Task → Spec (via frontmatter):**
```yaml
---
issue_number: TASK-003
type: task
spec: SPEC-001        # Links task to spec
status: todo
---
```

**Spec → Tasks (via Tasks section):**
```markdown
## Tasks
- [ ] TASK-001: User Registration
- [ ] TASK-002: Login Flow
- [ ] TASK-003: Password Reset  ← Added to spec file
```

---

## Plan Execution Methodology

**Pattern Used by `/plan`, `/implement`, `/advise` Commands**

### When to Create Plans

Use `/plan TASK-###` or `/plan BUG-###` to create implementation breakdown:
- Before starting implementation
- To validate approach with code-architect
- To generate scenario-driven test phases
- To estimate complexity

**Plans are optional but recommended for:**
- Complex tasks (>5 story points estimated)
- Tasks requiring architectural review
- Tasks with parent spec acceptance scenarios
- First-time implementations in a domain

### Plan Creation Process

**Command:** `/plan TASK-001`

**Steps:**

1. **Load Context**
   - Read TASK.md or BUG.md
   - If TASK has `spec: SPEC-###` in frontmatter:
     - Read parent spec
     - Extract acceptance scenarios
     - Note which scenarios are relevant

2. **Deep Thinking** (Sequential reasoning)
   - Analyze problem and requirements
   - Consider technical approach options
   - Identify research needs (libraries, patterns, best practices)
   - Think about acceptance scenarios:
     - How will we validate each scenario?
     - What test setup is needed?
     - Are there edge cases beyond the scenarios?

3. **Research** (if needed)
   - Library documentation (latest patterns)
   - Best practices for the domain
   - Security considerations

4. **Architectural Review** (code-architect agent)
   - System-wide design validation
   - Technology choices
   - Cross-cutting concerns

5. **Security Review** (if security-relevant)
   - Authentication/authorization patterns
   - Data protection
   - OWASP Top 10 considerations

6. **Generate Phases**
   - Break work into test-first phases
   - If parent spec has acceptance scenarios:
     - Generate test phases covering each scenario
     - Create explicit traceability (scenario → phase)
   - If no parent spec:
     - Generate tests from TASK.md acceptance criteria
   - Include complexity estimates per phase

7. **Scenario Coverage** (if spec exists)
   - Map which phases validate which scenarios
   - Verify all scenarios are covered
   - Highlight any gaps

8. **Present Plan**
   - Display phase breakdown
   - Show scenario coverage mapping (if applicable)
   - Present research summary
   - Show review signoffs
   - Display estimated complexity

**Example Output:**

```markdown
Creating implementation plan for TASK-001...

✓ Loaded TASK-001: User Login Flow
✓ Loaded parent spec SPEC-001: User Authentication
✓ Found 3 acceptance scenarios in SPEC-001

Research completed:
- Library: express-jwt (latest patterns)
- Best practices: JWT rotation, httpOnly cookies

Phase breakdown:

## Phase 1: Authentication Backend
- [ ] 1.1 Design JWT token structure
- [ ] 1.2 Implement user model with bcrypt
- [ ] 1.3 Create login endpoint (/api/auth/login)
- [ ] 1.4 Add JWT generation and validation
- [ ] 1.5 Write authentication integration tests

## Scenario Coverage

✓ SPEC-001 Scenario 1: "User logs in successfully"
  → Covered by Phase 1.5 (integration tests validate successful login flow)

✓ SPEC-001 Scenario 2: "Invalid password attempt"
  → Covered by Phase 1.5 (error handling tests for invalid credentials)

✓ SPEC-001 Scenario 3: "Inactive account login"
  → Covered by Phase 1.3 (account status checks before token generation)

All scenarios covered ✓

Reviews:
✓ code-architect: Architectural alignment confirmed
✓ security-auditor: JWT approach approved, suggested httpOnly cookies

Estimated complexity: 13 points (Medium)

Next: /implement TASK-001 1.1
```

### Complexity Scoring System

**Scoring Criteria (1-10 scale):**

**Low (1-3 points):**
- Single file changes
- Well-defined requirements
- No external dependencies
- Minimal testing complexity

**Medium (4-6 points):**
- Multi-file changes
- Some ambiguity in requirements
- External library integration
- Moderate test coverage needed

**High (7-10 points):**
- Architectural changes
- Significant ambiguity
- Multiple system integration
- Complex test scenarios
- Security/performance critical

**Complexity Indicators:**
- File count affected
- New dependencies required
- Test complexity (unit/integration/e2e)
- External integrations
- Performance requirements
- Security requirements

**Decomposition Threshold:** Tasks >8 points should be considered for decomposition into multiple tasks.

### Scenario-Driven Planning

**When to use:** Task references parent SPEC with acceptance scenarios

**How it works:**
1. `/plan` reads parent SPEC.md and extracts acceptance scenarios
2. Generates test phases that validate each scenario
3. Creates explicit traceability: Scenario → Phase → Tests

**Benefits:**
- Direct traceability from feature spec → implementation plan
- Test phases align with feature-level requirements
- Easy to verify all scenarios are covered before implementation
- Reduces risk of missing critical use cases

**Flexibility:** If no parent spec exists, generate tests from TASK.md acceptance criteria instead.

### Test-First Patterns

**Default Approach:** Test-first methodology embedded in phases

**Phase Pattern:**
```
Phase 1: Feature Name
- [ ] 1.1 Write tests for core functionality
- [ ] 1.2 Implement core functionality (make tests pass)
- [ ] 1.3 Refactor for clarity/performance
```

**Alternative Patterns (noted in PLAN.md):**
- **Strict TDD:** Red-Green-Refactor cycle visible in every step
- **BDD Scenarios:** Define Given/When/Then → Implement tests → Build features
- **Test Pyramid:** Heavy unit, moderate integration, light E2E
- **Pragmatic:** Spike/explore → Write tests → Implement production code

**Choice:** Teams can adapt to their preferred approach. Phases are suggestions.

### Progress Tracking Protocol

**Used by `/implement` command during execution:**

**After Each Phase:**
1. Update PLAN.md checkboxes (mark completed steps with `[x]`)
2. Add WORKLOG.md entry (what was done, decisions made)
3. Verify tests pass
4. Run code reviews if needed
5. Proceed to next phase

**Visibility:** Updated PLAN.md shows real-time progress to team.

### Agent Briefing

**Context Filtering for Implementation Agents:**

When `/implement` invokes specialist agents (backend-specialist, frontend-specialist, etc.), it provides filtered context:

**Domain-Specific Files Only:**
- Backend: Server files, API routes, database models
- Frontend: Components, pages, styles, hooks
- Database: Schemas, migrations, seeds

**Briefing Format:**
```
Task: TASK-001 Phase 1.2 - Implement user model with bcrypt
Spec Context: SPEC-001 Scenario 1 requires successful login validation
Test Requirements: Phase 1.5 will validate this implementation
Relevant Files: [filtered list for backend domain]
```

**Goal:** Reduce noise, increase focus on relevant context.

### Agile Plan Updates

**Plans are living documents.** Update during implementation when:

- Code reviews reveal better approaches
- Security audits require changes
- Complexity higher/lower than estimated
- New requirements discovered

**Protocol:**
1. Update PLAN.md phases
2. Add WORKLOG.md entry explaining why
3. Continue implementation

**Note in PLAN.md template:**
> "Phases are suggestions. Adapt based on code reviews and security audits."

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

## Common Patterns

### Pattern: Phased Delivery Spec

```
SPEC-001: "Payment Processing"

Phase 1 (MVP):
- TASK-001: Credit Card Processing (Stripe)
- TASK-002: Basic Payment Form
- TASK-003: Receipt Generation

Phase 2 (Enhancements):
- TASK-004: PayPal Integration
- TASK-005: Saved Payment Methods
- TASK-006: Refund Processing
```

**Approach:** Create all tasks upfront, mark MVP tasks as higher priority.

### Pattern: Bug Fix Spec

```
SPEC-002: "Session Management Improvements"

- BUG-001: Session Timeout Not Working
- BUG-002: Session Cookies Not Secure
- TASK-007: Add Session Monitoring
- TASK-008: Session Cleanup Cron Job
```

**Approach:** Mix bugs and improvement tasks in single spec.

### Pattern: Spike Before Implementation

```
SPEC-003: "Real-time Notifications"

1. SPIKE-001: Research WebSocket vs SSE
2. SPIKE-002: Evaluate Pusher vs self-hosted
3. TASK-009: Implement chosen solution
```

**Approach:** Research tasks before implementation tasks. (Requires custom SPIKE issue type - see pm/templates/README.md for adding custom types.)

---

## Related Documentation

**Templates (Source of Truth for File Structure):**
- `pm/templates/spec.md` - Feature spec template with YAML config
- `pm/templates/task.md` - Task template with YAML config
- `pm/templates/bug.md` - Bug template with YAML config
- `pm/templates/plan.md` - Plan template with YAML config

**User Guides:**
- `pm/README.md` - Overview of PM directory for developers

**Workflow Guides:**
- `development-loop.md` - Implementation workflow and quality gates
- `worklog-format.md` - WORKLOG entry formats
- `git-workflow.md` - Branch naming aligned with issue IDs

**External Integration:**
- `jira-integration.md` - Jira mode, field discovery, promotion workflows

**Commands:**
- `/spec` - Creates specs and tasks following these patterns
- `/plan` - Creates PLAN.md with complexity scoring and scenario coverage
- `/implement` - Executes plans with progress tracking
- `/advise` - Collaborative implementation mode
- `/adr` - Create Architecture Decision Records for complex decisions
