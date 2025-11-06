---
# === Metadata ===
template_type: "guideline"
created: "2025-11-05"
last_updated: "2025-11-06"
status: "Active"
target_audience: ["AI Assistants", "Project Managers"]
description: "PLAN.md structure, phase patterns, review requirements, and progress tracking protocols"

# === Configuration ===
plan_structure: "phase_based"              # Phases with nested subtasks
review_mandatory: true                     # Code-architect MUST review all plans
security_review_auto: true                 # Auto-detect security-relevant tasks
progress_tracking: "dual_system"           # PLAN.md (phases) + TASK.md (acceptance criteria)
---

# PLAN.md Structure and Planning Guidelines

**Purpose**: Define implementation phases, track progress, and ensure architectural soundness

**Referenced by**: `/plan` command (creation), `/implement` command (execution and updates)

## Quick Reference

**What PLAN.md tracks**: Implementation phases (HOW the work will be done)
**What TASK.md tracks**: Acceptance criteria (WHAT requirements must be met)

**Review gates**:
1. Code-architect (ALWAYS)
2. Security-auditor (IF security-relevant)

**Progress updates**: After each phase completion, mark checkbox in PLAN.md

---

## Implementation Plan Structure

### Default Phase Structures by Task Type

**Standard Implementation:**
1. Design → Test-Driven Implementation → Integration → Documentation

**Frontend Tasks:**
1. Design → Component Tests → Implementation → Responsive/E2E

**Backend Tasks:**
1. API Design → Unit Tests → Implementation → Integration Tests

**Bug Fixes:**
1. Investigation → Root Cause → Fix → Regression Tests

**Database Tasks:**
1. Schema Design → Migration Script → Unit Tests → Integration Tests

**Security Tasks:**
1. Threat Modeling → Security Tests → Implementation → Security Audit

### Alternative Test-First Patterns

Teams can choose different testing approaches based on context:

- **Strict TDD**: Red-Green-Refactor cycle visible in every step
- **BDD Scenarios**: Given/When/Then scenarios → Implement tests → Build features
- **Test Pyramid**: Heavy unit tests, moderate integration, light E2E
- **Pragmatic**: Spike/explore → Write tests → Implement production code

**Note**: Phases are suggestions. Modify to fit your workflow and team preferences.

### Phase Numbering

**Format**: `Major.Minor` (e.g., 1.1, 1.2, 2.1)

**Major number**: Logical grouping of related work
**Minor number**: Sequential subtasks within that group

**Example**:
```markdown
## Phase 1: Backend Foundation
- [ ] 1.1 Design database schema
- [ ] 1.2 Write schema tests
- [ ] 1.3 Implement models
- [ ] 1.4 Write API tests
- [ ] 1.5 Implement API endpoints

## Phase 2: Frontend Integration
- [ ] 2.1 Create React components
- [ ] 2.2 Write component tests
- [ ] 2.3 Integrate with API
- [ ] 2.4 Add E2E tests
```

---

## Plan Review Requirements

**Referenced by**: `/plan` command before presenting plan to user

### Mandatory Code-Architect Review

**BEFORE presenting any plan to the user**, the `/plan` command must invoke the code-architect agent for review.

**Code-Architect Reviews:**
- Architectural soundness and consistency with existing ADRs
- Phase structure and logical breakdown
- Technology choices and patterns
- Scalability and maintainability considerations
- Cross-cutting concerns (security, performance, observability)
- Integration with existing system architecture

**Code-Architect May:**
- Approve plan as-is (proceed to present to user)
- Suggest modifications to phases or approach
- Request additional phases for technical debt or infrastructure
- Recommend creating ADR for significant architectural decisions
- Identify potential architectural risks or anti-patterns

### Conditional Security-Auditor Review

**AFTER code-architect approval**, the `/plan` command must detect if the task is security-relevant and invoke security-auditor for review.

**Auto-Detection Criteria** (task is security-relevant if ANY match):

**Keywords in task description or acceptance criteria:**
- Authentication/authorization: `auth`, `login`, `password`, `token`, `session`, `permission`, `role`, `access control`
- Data security: `encrypt`, `decrypt`, `hash`, `salt`, `crypto`, `PII`, `sensitive data`, `personal information`
- Security features: `security`, `vulnerability`, `threat`, `OWASP`, `XSS`, `CSRF`, `SQL injection`, `sanitize`, `validate input`
- Admin features: `admin`, `privilege`, `elevation`, `sudo`, `root`
- External integrations: `API key`, `secret`, `credential`, `OAuth`, `SAML`, `SSO`, `third-party`

**File patterns in affected files:**
- `**/auth/**`, `**/authentication/**`, `**/authorization/**`
- `**/security/**`, `**/crypto/**`, `**/encryption/**`
- `**/*Auth*`, `**/*Security*`, `**/*Crypto*`, `**/*Validation*`
- `**/middleware/auth*`, `**/guards/**`, `**/policies/**`

**Security-Auditor Reviews:**
- Threat modeling for authentication/authorization flows
- Input validation and sanitization approach
- Cryptographic implementation (algorithms, key management)
- Data protection for sensitive information (PII, credentials)
- Authorization logic and permission models
- Security best practices (OWASP compliance)
- Vulnerability risks (injection, XSS, CSRF, etc.)

**Security-Auditor May:**
- Approve security approach as-is
- Suggest security-specific phases (threat modeling, penetration testing)
- Request additional security controls or validation
- Recommend creating security-focused ADR
- Identify potential security vulnerabilities in plan
- Require security testing phases before implementation

**Only after BOTH code-architect AND security-auditor approval (if security-relevant) should the plan be presented to the user.**

---

## Progress Tracking Protocol

**Referenced by**: `/implement` command after each phase completion

### Dual Tracking System

**PLAN.md**: Tracks implementation phases (what work needs to be done)
**TASK.md/BUG.md**: Tracks acceptance criteria (what requirements must be satisfied)

### After Each Phase Completion

**Execute in this order:**

**1. Verify Completion Thoroughly**
   - All tests pass (run test suite)
   - Code works as intended (manual verification if needed)
   - Requirements from phase description fully met
   - No errors, warnings, or broken functionality

**2. Update PLAN.md (Phase Tracking)**
   - Change `- [ ] 1.1 Task description` to `- [x] 1.1 Task description`
   - ONLY mark complete when verified - never mark prematurely
   - Use Edit tool to update the specific checkbox
   - Update IMMEDIATELY after completion, not in batches

**3. Update TASK.md/BUG.md (Acceptance Criteria Tracking)**
   - **For local issues (TASK-###, BUG-###)**:
     - Review acceptance criteria checkboxes in TASK.md/BUG.md
     - If phase satisfies any criterion, mark complete: `- [ ] criterion` → `- [x] criterion`
     - Example: Phase "1.2 Implement login form" satisfies "User can log in with email/password"
     - ONLY check off when criterion fully satisfied and verified
   - **For Jira issues (PROJ-###)**:
     - Note satisfied criteria in WORKLOG entry (Jira is source of truth)
     - Example: "✓ Satisfies Jira AC: User can log in with email/password"

**4. Write WORKLOG Entry (at agent handoffs only)**
   - **When**: Only when handing off to another agent OR when phase fully complete
   - **Type**: HANDOFF entry (if passing to another agent) or COMPLETE entry (if phase done)
   - Get timestamp: Run `date '+%Y-%m-%d %H:%M'` (never estimate)
   - Document what YOU did in this step (not entire phase summary - keep it 5-10 lines)
   - Note which agent receives work next (if handoff)
   - Prepend to top (reverse chronological order)
   - See `worklog-format.md` for entry patterns
   - Include decision rationale in WORKLOG entries

**5. Architecture Decisions**
   - For complex architectural decisions affecting multiple components, use `/adr` command
   - ADRs document context, decision, alternatives, and consequences
   - Reference ADRs from WORKLOG entries or code comments

**Critical**: Never mark items complete until verified working. Premature checkoffs lead to incomplete work and confusion.

---

## Task Completion Validation

**Referenced by**: `/implement` command when all PLAN.md phases are checked off

### Completion Checklist

**Required before marking any task complete:**

**1. All PLAN.md Phases Checked Off**
   - Verify EVERY phase checkbox is marked: `- [x] All phases`
   - If any phases remain unchecked, task is NOT complete

**2. All Acceptance Criteria Verified and Checked Off**
   - **For local issues (TASK-###, BUG-###)**:
     - EVERY checkbox in TASK.md/BUG.md acceptance criteria marked: `- [x] All criteria`
   - **For Jira issues (PROJ-###)**:
     - EVERY Jira acceptance criterion verified (documented in WORKLOG)
   - If any criteria remain unsatisfied, task is NOT complete

**3. Tests Passing**
   - All test suites pass with 95%+ coverage (or configured target)
   - Run full test suite before marking complete
   - No failing tests, no errors, no warnings

**4. WORKLOG Documented**
   - Final entry summarizing overall task completion
   - Lists all completed phases and satisfied criteria
   - See `worklog-format.md` for format

**5. Epic Consistency (if epic exists)**
   - Task marked complete in epic task list: `- [x] TASK-001`
   - Epic progress updated

**Final Checkpoint**: Can you honestly say this task is 100% complete with all requirements met? If no, keep working. If yes, mark complete.

---

## Test-First Guidance Protocol

**Referenced by**: `/implement` command before implementation phases

### Pragmatic Test-First Philosophy

**Test-first when you know what to build, code-first when discovering unknowns.**

### Pre-Implementation Check

**Before executing implementation phases (e.g., "2.2 Implement X"):**

**1. Check for Test Phases**
   - Look for preceding test phase (e.g., "2.1 Write tests for X")
   - If test phase exists but is unchecked:
     - **Prompt**: "Pragmatic test-first: Phase '2.1 Write tests' should typically come first. Execute 2.1 first? (yes/skip)"
     - **NEVER BLOCK**: Always allow skip - user may be in discovery mode

**2. If No Tests Found**
   - **Prompt with options**:
     1. Auto-generate comprehensive test suite from acceptance criteria (RECOMMENDED if requirements are clear)
     2. I'll write tests manually first (good for learning/experimentation)
     3. Skip tests for now - I'm in discovery mode (must add tests before phase completion)
   - **Default**: Option 1 if no user input
   - **Track choice**: Document in WORKLOG entry

### AI-Powered Test Generation Messaging

**Frame test generation as AI superpower, not chore:**
- "✅ Generated comprehensive test suite with X unit tests, Y integration tests, Z edge cases. All tests documented for team reference."
- Highlight: Create comprehensive test suites in seconds with explanatory comments
- Benefit: Makes TDD/BDD easier than skipping tests!

---

## Agent Context Briefing

**Referenced by**: `/implement` command when invoking specialist agents

### Principle

**Provide domain-specific context to agents, not full epic context dump.**

Filter and prepare only relevant information for each specialist to optimize performance and reduce context overload.

### Context Filtering Patterns by Agent

**Backend Specialists** receive:
- API contracts, database schemas
- Security requirements, performance targets
- Relevant ADR decisions for backend architecture
- Previous backend work from WORKLOG

**Frontend Specialists** receive:
- Component specifications, state management patterns
- UI/UX requirements, responsive design needs
- Relevant ADR decisions for frontend architecture
- Previous frontend work from WORKLOG

**Test Engineers** receive:
- Coverage targets, validation patterns
- Quality gates, existing test structure
- Test-first approach configuration
- Previous testing work from WORKLOG

**Security Auditors** receive:
- Threat models, authentication flows
- Authorization requirements, compliance needs
- Security-related ADR decisions
- Previous security work from WORKLOG

**Database Specialists** receive:
- Schema requirements, migration patterns
- Performance constraints, data validation
- Database-related ADR decisions
- Previous database work from WORKLOG

**Performance Optimizers** receive:
- Performance targets, current bottlenecks
- Scaling requirements, optimization opportunities
- Performance-related ADR decisions
- Previous optimization work from WORKLOG

### Dynamic Context Loading

**Process:**
1. Parse WORKLOG.md and ADR files in real-time
2. Extract only domain-relevant sections for selected agent
3. Combine with phase-specific requirements from PLAN.md
4. Include lessons learned from previous phases to avoid repeating mistakes
5. Present concise, actionable context that eliminates noise

**Benefit**: Agents focus on relevant information without context overload, improving decision quality and execution speed.

---

## Related Documentation

**For PLAN.md file creation**:
- See `issue-management.md` for complete PLAN.md file format
- See `pm-guidelines.md` for epic and issue structure

**For progress updates**:
- See `worklog-format.md` for WORKLOG entry formats
- See `development-loop.md` for quality gates and workflow

**For agent coordination**:
- See `agent-coordination.md` for agent escalation paths
- See `development-loop.md` for agent handoff patterns

**For testing approach**:
- See `testing-standards.md` for test philosophy
- See `development-loop.md` for TDD/BDD patterns
