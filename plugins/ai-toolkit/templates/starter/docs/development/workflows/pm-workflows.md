---
# === Metadata ===
template_type: "guideline"
created: "2025-11-18"
last_updated: "2025-11-18"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "PM workflows for spec creation, task creation, and plan execution"
---

# PM Workflows

**Referenced by Commands:** `/spec`, `/plan`, `/implement`, `/advise`

## Overview

This guide defines workflows for creating and executing project management work. For file structure and naming conventions, see **pm-file-formats.md**.

---

## File Purpose and Content Boundaries

**Clear separation between PM artifacts (what/why) and implementation artifacts (how).**

### SPEC.md - Feature Specification

**Purpose:** Define WHAT feature/capability we're building and WHY it's valuable

**Contains:**
- Feature name and description (user perspective)
- Acceptance scenarios (2-5 key scenarios in Given-When-Then BDD format)
- Definition of done (completion criteria)
- Out of scope (explicit boundaries)
- Task list (references to TASK-### items)

**Who creates:** Product/PM via `/spec` command or `/jira-epic` (Jira mode)
**Who manages:** Product/PM (rarely edited during implementation)
**Syncs with:** Jira (if Jira mode enabled)
**Living document:** Yes - use `/spec --update SPEC-###` to sync with implementation reality

**Key principle:** Describes the feature capability from user/business perspective, not implementation details

**Example:**
```markdown
# SPEC-001: User Authentication

## Description
Enable users to securely log in, register, and reset passwords...

## Acceptance Scenarios
- Scenario: User logs in successfully...
- Scenario: Invalid password attempt...
```

---

### TASK.md / BUG.md - Work Item Definition

**Purpose:** Define WHAT specific work needs to be done and WHY

**Contains:**
- Task/bug description (clear problem statement)
- Acceptance criteria (specific, testable checklist)
- Technical notes (constraints, gotchas, ADR references)
- Parent spec reference (in YAML frontmatter: `spec: SPEC-001`)

**Who creates:** PM/developers via `/spec` task creation or manual creation
**Who manages:** PM (acceptance criteria rarely change during implementation)
**Syncs with:** Jira/Linear/GitHub Issues (if external PM tool configured)
**Living document:** No - acceptance criteria stay stable (update in spec instead)

**Scope:** Deployable change (merged to main as complete unit), typically 1-3 days, 3-8 phases

**Key principle:** Defines work item from PM perspective - WHAT needs to be built and acceptance criteria

**Example:**
```markdown
# TASK-001: User Login Flow

## Description
Implement JWT-based login endpoint with session management...

## Acceptance Criteria
- [ ] User can log in with valid email/password
- [ ] Invalid credentials return 401 error
- [ ] JWT token includes user ID and role
```

---

### PLAN.md - Implementation Breakdown

**Purpose:** Define HOW the work will be implemented (strategic phases, not prescriptive steps)

**Contains:**
- Phase-based breakdown (Phase 1: Setup, Phase 2: Core logic, Phase 3: Tests)
- Checkboxed implementation steps (strategic objectives, not tactical commands)
- Scenario coverage mapping (which phases validate which acceptance scenarios)
- Complexity analysis (score, indicators, decomposition recommendations)
- Implementation philosophy (strategic vs tactical, living document reminders)

**Who creates:** AI via `/plan TASK-###` or `/plan BUG-###` command
**Who manages:** AI and developers during implementation (updated as discoveries are made)
**Syncs with:** Nothing (purely local file, never synced to external PM tools)
**Living document:** Yes - updated when phase discoveries affect later phases

**Key principle:** Strategic plan (WHAT to build in each phase), not tactical prescription (HOW to code it)

**Example:**
```markdown
# Implementation Plan: TASK-001 User Login Flow

## Phase 1 - Authentication Backend
- [ ] 1.1 Implement user model with password hashing
- [ ] 1.2 Create login endpoint with JWT generation
- [ ] 1.3 Write authentication integration tests

## Scenario Coverage
✓ SPEC-001 Scenario 1: "User logs in successfully"
  → Covered by Phase 1.3 (integration tests)
```

---

### Content Decision Matrix

**Use this to decide where content belongs:**

| Content Type | SPEC.md | TASK.md | PLAN.md |
|-------------|---------|---------|---------|
| Feature description (user perspective) | ✅ | ❌ | ❌ |
| Acceptance scenarios (BDD format) | ✅ | ❌ | ❌ |
| Work item description | ❌ | ✅ | ❌ |
| Acceptance criteria (checklist) | ❌ | ✅ | ❌ |
| Technical constraints/notes | ❌ | ✅ | ❌ |
| Phase breakdown | ❌ | ❌ | ✅ |
| Implementation steps (checkboxes) | ❌ | ❌ | ✅ |
| Scenario coverage mapping | ❌ | ❌ | ✅ |
| Complexity analysis | ❌ | ❌ | ✅ |
| Parent spec reference | ❌ | ✅ | ✅ |

**Key Distinctions:**

1. **SPEC vs TASK:**
   - SPEC = Feature-level (multi-task), user perspective, BDD scenarios
   - TASK = Work-item-level (single deployable change), acceptance criteria checklist

2. **TASK vs PLAN:**
   - TASK = WHAT needs to be done (from PM perspective, stable)
   - PLAN = HOW it will be done (from implementation perspective, evolves)

3. **Syncing:**
   - SPEC.md syncs with Jira epics (if Jira enabled)
   - TASK.md syncs with Jira issues (if Jira enabled)
   - PLAN.md NEVER syncs (purely local, AI-managed)

4. **Living Documents:**
   - SPEC.md: Yes (use `/spec --update` after task completion)
   - TASK.md: No (acceptance criteria stay stable)
   - PLAN.md: Yes (updated as implementation progresses)

**Example Scenario:**

```
SPEC-001: User Authentication (feature-level)
├── Acceptance Scenario: "User logs in successfully" (BDD format)
├── TASK-001: User Login Flow (work item)
│   ├── Acceptance Criteria: "User can log in with valid credentials"
│   └── PLAN.md (implementation phases)
│       ├── Phase 1: Backend (tests, model, endpoint)
│       └── Scenario Coverage: Maps to SPEC-001 scenarios
└── TASK-002: Password Reset Flow (work item)
    └── PLAN.md (separate implementation plan)
```

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

### Task and Phase Scoping Principles

**CRITICAL:** Proper scoping ensures smooth development flow and clean git history.

**Task Scope = Deployable Change:**
- A task should represent work that gets **merged to main as a complete unit**
- Think: "What's the smallest valuable change I can deploy?"
- Tasks should be **independently deployable** (can merge without breaking main)
- Typical size: 1-3 days of focused work, 3-8 phases

**Examples of Properly Scoped Tasks:**
- ✅ "Implement user login with JWT authentication" (complete feature, deployable)
- ✅ "Add password reset flow with email notifications" (end-to-end feature)
- ✅ "Create admin dashboard with role-based access" (deployable admin capability)

**Examples of Tasks That Are Too Small:**
- ❌ "Create user model" (not deployable alone)
- ❌ "Add bcrypt to dependencies" (not a feature)
- ❌ "Write login endpoint tests" (part of larger work)

**Phase Scope = Logical Commit Point:**
- A phase represents work that gets **committed as a logical unit**
- Each phase should be **testable, reviewable, and committable**
- Phases are steps within a task, not standalone work items
- Typical size: 1-4 hours of focused work

**Examples of Properly Scoped Phases:**
- ✅ "1.1 Implement user model with password hashing"
- ✅ "1.2 Create login endpoint with JWT generation"
- ✅ "1.3 Write authentication integration tests"

**Key Question:** If a task feels too small to deploy, it's probably a phase within a larger task.

### When to Create Tasks vs Bugs

**Tasks:**
- New features or enhancements
- Implementation work
- Keywords: "add", "implement", "create", "build"

**Bugs:**
- Defects, regressions, broken functionality
- Keywords: "fix", "bug", "broken", "regression"

**Detection:** Commands infer type from conversation, but users can specify explicitly.

### Template-Driven Creation

**Process:**
1. Determine issue type (TASK, BUG, etc.)
2. Read corresponding template (templates/[type].md)
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

### Test-First Phase Loop

**MANDATORY:** Every phase follows strict test-first loop

**Phase Execution Loop:**
```
For each phase:
  1. Write tests for phase (MUST fail - red)
  2. Write code to pass tests (green)
  3. Run code review (MUST pass ≥90)
  4. Only when ALL pass:
     - Commit phase changes
     - Update WORKLOG entry
     - Move to next phase
```

**Quality Gates (all MUST pass before proceeding):**
- ✅ Tests written and initially failing (confirms tests are valid)
- ✅ Tests passing after implementation
- ✅ Code review score ≥90

**Loop Example:**
```
Phase 1.1: Implement user model with password hashing

Step 1 - Write Tests (Red):
  - Create test file: user.test.ts
  - Write tests for user creation, password hashing, validation
  - Run tests → MUST FAIL (red)
  - If tests pass immediately, they're not testing anything new

Step 2 - Write Code (Green):
  - Implement User model
  - Implement password hashing
  - Run tests → MUST PASS (green)

Step 3 - Code Review:
  - Run code-reviewer agent
  - Score MUST be ≥90
  - Address any critical issues before proceeding

Step 4 - Commit & Document:
  - Commit: "feat: implement user model with bcrypt password hashing"
  - WORKLOG entry: What was done, decisions made, lessons learned
  - Mark phase 1.1 complete in PLAN.md

Only then → Move to Phase 1.2
```

**Why This Loop:**
- **Tests fail first**: Proves tests are testing something real
- **Code review mandatory**: Catches issues before they compound
- **Commit per phase**: Clean git history, easy rollback
- **WORKLOG per phase**: Context for future phases and developers

**No Shortcuts:** Do not skip tests, do not skip code review, do not combine phases.

### Progress Tracking Protocol

**Used by `/implement` command during execution:**

**Phase Completion Checklist (ALL required):**
1. ✅ Tests written and initially failing (red)
2. ✅ Tests passing after implementation (green)
3. ✅ Code review completed with score ≥90
4. ✅ Phase changes committed to git
5. ✅ WORKLOG.md entry added (what was done, decisions made, lessons learned)
6. ✅ PLAN.md checkboxes updated (mark completed steps with `[x]`)

**Only then:** Proceed to next phase

**Enforcement:** `/implement` command enforces this loop. If any quality gate fails, phase is not considered complete.

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
- **Previous phase discoveries affect future phases**
- **Implementation reveals better sequencing**

**Protocol:**
1. Read WORKLOG.md for context on what's been done and lessons learned
2. Update PLAN.md phases (what changed and why)
3. Add WORKLOG.md entry explaining plan update rationale
4. Continue implementation with updated plan

**Key Principles:**

**Strategic, Not Tactical:**
- PLAN.md describes **WHAT** to build (objectives, outcomes)
- Specialist agents decide **HOW** to build it (implementation details)
- Implementation details adapt to current codebase state
- Agents leverage WORKLOG for lessons learned and context

**Examples:**
- ✅ Strategic: "1.2 Implement user model with password hashing"
- ❌ Tactical: "1.2 Create User class with bcrypt.hash() in the setPassword method using 10 salt rounds"

**Why Strategic Wins:**
- Specialist agents see current code state and can adapt
- WORKLOG provides context about what worked/didn't work
- Flexibility for better approaches discovered during implementation
- Avoids outdated prescriptive steps

**WORKLOG Integration:**
- Agents read WORKLOG before each phase to understand context
- Past decisions, gotchas, and lessons inform current implementation
- If Phase 1 revealed "bcrypt is too slow, switched to argon2", Phase 2 agent knows this

**Note in PLAN.md template:**
> "Phases are suggestions. Adapt based on code reviews, security audits, and implementation discoveries."

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

**Approach:** Research tasks before implementation tasks. (Requires custom SPIKE issue type - see templates/README.md for adding custom types.)

---

## Related Documentation

**File Formats:**
- `pm-file-formats.md` - File structure, naming conventions, directory organization

**Templates (Source of Truth for File Structure):**
- `templates/spec.md` - Feature spec template with YAML config
- `templates/task.md` - Task template with YAML config
- `templates/bug.md` - Bug template with YAML config
- `templates/plan.md` - Plan template with YAML config
- `templates/README.md` - Template usage guide and custom types

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
