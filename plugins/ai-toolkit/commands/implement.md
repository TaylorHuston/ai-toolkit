---
tags: ["workflow", "development", "execution"]
description: "Execute specific implementation phases from task plans with test-first enforcement"
argument-hint: "TASK-### PHASE | BUG-### PHASE | PROJ-### PHASE"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/development-loop.md  # Test-first, quality gates, WORKLOG, progress tracking, context briefing
  - docs/development/guidelines/git-workflow.md  # Branch creation and verification
---

# /implement Command

Execute specific phases of implementation plans with full context awareness and intelligent agent coordination.

## Usage

```bash
/implement TASK-001 1.1    # Execute phase 1.1 of TASK-001
/implement BUG-003 2.2     # Execute phase 2.2 of BUG-003
/implement PROJ-123 1.1    # Execute phase 1.1 of Jira issue
```

**IMPORTANT**: Both parameters are mandatory (Issue ID + Phase). Command will show available phases if parameters are missing or invalid.

## Agent Coordination

**Primary**: Domain specialists (backend-specialist, frontend-specialist, database-specialist, etc.) based on phase type
**Supporting**: test-engineer (test-first approach), code-reviewer (quality validation)
**Security**: security-auditor (automatic for security-relevant phases per `development-loop.md` detection criteria)
**Coordination**: WORKLOG.md for narrative work history and context preservation

## How It Works

### All Workflow Rules are in Guidelines

**This command orchestrates the development loop defined in** `docs/development/guidelines/development-loop.md`:
- **Test-First Guidance**: Pragmatic test-first prompting protocol
- **Progress Tracking**: Checkbox management for PLAN.md and TASK.md
- **WORKLOG Format**: Entry structure, timestamp requirements, completeness criteria
- **Quality Gates**: Per-phase validation requirements
- **Task Completion**: Comprehensive completion checklist
- **Agent Context Briefing**: Domain-specific context filtering patterns

**The command reads these configurations and enforces the workflow your team has defined.**

### Execution Flow

**1. Parameter Validation**
- Detect ID type: TASK-###/BUG-### (local) vs PROJ-### (Jira)
- Find issue directory or fetch from Jira
- Locate PLAN.md and validate phase exists
- If invalid: Show available phases

**2. Branch Verification**
- Read `git-workflow.md` for branch configuration
- Check current branch matches expected (feature/TASK-001, bugfix/BUG-003, etc.)
- Offer to create/switch if mismatch (non-blocking warning)

**3. Load Context**
- **Local issues**: Read TASK.md/BUG.md, epic context, ADRs
- **Jira issues**: Fresh fetch from Jira (description, criteria, status), read ADRs
- Read WORKLOG.md for previous work and lessons learned
- Read RESEARCH.md for technical decisions

**4. Test-First Check**
- **Following** `development-loop.md` **test-first guidance protocol**
- Check for preceding test phases
- Prompt user if tests should come first (never blocking)
- Offer auto-generation of comprehensive test suites

**5. Agent Selection and Context Briefing**
- Select specialist based on phase domain
- **Following** `development-loop.md` **agent context briefing patterns**
- Provide filtered, domain-specific context (not full epic dump)
- Include relevant WORKLOG insights and ADR decisions

**6. Phase Execution**
- Agent follows development loop: test → code → review iteration
- **Following** `development-loop.md` **quality gates**
- Iterate until code review score ≥ 90 (or configured threshold)
- **Agent writes WORKLOG HANDOFF entry before invoking code-reviewer**

**7. Code Review Handoff**
- **BEFORE invoking code-reviewer**: Implementing agent MUST write WORKLOG entry
- Entry format: `YYYY-MM-DD HH:MM - agent-name → code-reviewer`
- Brief summary (5-10 lines): what was done, files changed, gotchas
- Include handoff note: `→ Passing to code-reviewer for {reason}`
- Code-reviewer executes review and returns result
- **IF changes required**: Code-reviewer writes WORKLOG entry with issues found
- **IF approved**: Continue to security review (if needed) or post-phase updates

**8. Security Review (CONDITIONAL)**
- **Following** `development-loop.md` **security-relevant phase detection**
- Auto-detect if phase is security-relevant (keywords, file patterns)
- If security-relevant: Invoke security-auditor before phase completion
- **Agent writes WORKLOG HANDOFF entry before invoking security-auditor**
- Security-auditor reviews implementation for vulnerabilities
- **BLOCKS phase completion if critical security issues found**
- Iterate on security fixes until approved (with WORKLOG entries at each handoff)

**9. Post-Phase Updates**
- **Following** `development-loop.md` **progress tracking protocol**
- Verify completion thoroughly (tests pass, code works, requirements met, security approved if applicable)
- Update PLAN.md checkbox: `- [ ] 1.1` → `- [x] 1.1`
- Update TASK.md acceptance criteria if satisfied
- **Write WORKLOG COMPLETE entry** (format: `agent-name (Phase X.Y COMPLETE)`)
- Consider RESEARCH.md for complex decisions

**10. Task Completion Check**
- If all PLAN.md phases complete:
- **Following** `development-loop.md` **task completion validation**
- Verify ALL acceptance criteria checked off
- Run full test suite
- Write final WORKLOG entry
- Update epic if applicable

## Jira Integration

**Hybrid Mode**: Works with both local (TASK-###, BUG-###) and Jira (PROJ-###) issues.

### ID Detection
- Local: `TASK-###`, `BUG-###` (e.g., TASK-001, BUG-042)
- Jira: `[A-Z]+-###` (e.g., PROJ-123, ENG-456)

### Context Loading Strategy

**For Local Issues**:
- Read requirements from TASK.md/BUG.md
- Read PLAN.md for phase details
- Standard workflow (unchanged)

**For Jira Issues**:
- **Fresh fetch from Jira** (latest description, acceptance criteria, status)
- Read PLAN.md from local directory
- No local TASK.md (Jira is source of truth)
- Ensures requirements always current

### Branch Naming
- Local: `feature/TASK-001`, `bugfix/BUG-042`
- Jira: `feature/PROJ-123`, `bugfix/PROJ-456`

### Jira Workflow Example
```
User: /implement PROJ-123 1.1

AI: Fetching PROJ-123 from Jira...
    Issue: User Registration Form
    Status: In Progress

    Loading PLAN.md...
    Phase 1.1: Design database schema

    Checking branch...
    ✓ On feature/PROJ-123

    Following test-first protocol...
    [Executes phase per development-loop.md]

    ✓ Phase 1.1 complete
    ✓ Updated PLAN.md checkbox
    ✓ Updated WORKLOG.md

    Next: /implement PROJ-123 1.2
```

### Error Handling

**Issue not found in Jira**:
```
Error: PROJ-123 not found in Jira.
Check: https://company.atlassian.net/browse/PROJ-123
```

**MCP unavailable**:
```
Error: Atlassian MCP not available.
Options:
1. Configure Atlassian Remote MCP Server
2. Disable Jira in CLAUDE.md
3. Use local issue: /implement TASK-001 1.1
```

**No PLAN.md**:
```
Error: No PLAN.md found.
Run: /plan PROJ-123
```

**Invalid phase**:
```
Error: Phase '5.5' not found in TASK-001 plan.

Available phases:
  - [ ] 1.1 Write tests for authentication
  - [ ] 1.2 Implement authentication logic
  - [x] 2.1 Create database schema (completed)
  - [ ] 2.2 Add migration scripts
```

## Outputs

### MANDATORY Updates

**Following** `development-loop.md` **progress tracking protocol**, after each phase:

**pm/issues/ISSUE-ID-*/PLAN.md**:
- ✓ Mark completed phase: `- [ ] 1.1` → `- [x] 1.1`
- ✓ ONLY after verification (tests pass, code works)
- ✓ Update IMMEDIATELY after completion

**pm/issues/ISSUE-ID-*/TASK.md or BUG.md**:
- ✓ Mark completed acceptance criteria: `- [ ] criterion` → `- [x] criterion`
- ✓ For Jira issues: Note satisfied criteria in WORKLOG (Jira is source of truth)
- ✓ Update task status when ALL criteria complete

**pm/issues/ISSUE-ID-*/WORKLOG.md** (REQUIRED):
- ✓ Write HANDOFF entries when passing work between agents
- ✓ Write COMPLETE entry when phase fully done
- ✓ Timestamped entries (get timestamp via `date` command)
- ✓ Brief summaries (5-10 lines): what YOU did, not entire phase history
- ✓ See `development-loop.md` for stream-style format and patterns
- ✓ Prepend to top (reverse chronological)

**pm/issues/ISSUE-ID-*/RESEARCH.md** (if needed):
- ✓ Complex technical decisions requiring detailed rationale
- ✓ See `development-loop.md` for when to create

**pm/epics/EPIC-###-name.md** (if epic exists):
- ✓ Mark task complete when all phases done: `- [x] TASK-001`

### Validation Before Completion

**Following** `development-loop.md` **task completion validation checklist**:
- All PLAN.md phases checked off: `- [x] All phases`
- All acceptance criteria verified and checked off
- All tests pass with 95%+ coverage (or configured target)
- WORKLOG documents complete story
- Epic updated if applicable

**Critical**: Only mark complete when 100% done. Partial completion is not completion.

## Branch Management Integration

**Automatic branch creation:**
- Creates `feature/TASK-001` or `bugfix/BUG-003` if needed
- Reads configuration from `git-workflow.md`

**Branch verification (non-blocking):**
- Warns if on wrong branch
- Offers to switch/create correct branch
- Allows proceeding if user declines

**Why non-blocking:**
- Supports spike/exploration workflows
- Trusts user judgment for custom workflows

## Integration with Workflow

**Position**: After `/plan`, executing phases one at a time

- **After `/plan`**: Reads PLAN.md phases
- **During development**: Executes phases following development loop
- **Before merge**: All phases complete, all tests pass

## Implementation Notes

When implementing `/implement ISSUE-ID PHASE`:

1. **Validate parameters**: Issue exists, phase exists in PLAN.md
2. **Verify branch**: Warn if mismatch, offer to fix (non-blocking)
3. **Load context**: TASK.md/BUG.md or Jira, ADRs, WORKLOG, RESEARCH
4. **Check test-first**: Follow `development-loop.md` guidance protocol
5. **Brief agent**: Provide domain-filtered context per `development-loop.md`
6. **Execute phase**: Agent follows development loop with quality gates
7. **Update progress**: Follow `development-loop.md` progress tracking protocol
8. **Check completion**: If all phases done, follow `development-loop.md` task completion validation

**Key Principle**: Commands orchestrate, guidelines configure. All workflow rules live in `development-loop.md` for team customization.
