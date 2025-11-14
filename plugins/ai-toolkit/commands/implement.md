---
tags: ["workflow", "development", "execution"]
description: "Execute specific implementation phases from task plans with test-first enforcement"
argument-hint: "TASK-### PHASE | TASK-### --next | --next"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/pm-guide.md  # Test-first, progress tracking, agent briefing
  - docs/development/guidelines/worklog-format.md  # WORKLOG entry formats
  - docs/development/guidelines/development-loop.md  # Quality gates
  - docs/development/guidelines/git-workflow.md  # Branch creation and verification
---

# /implement Command

**WHAT**: Execute implementation phases with intelligent agent coordination and progress tracking.

**WHY**: Structured execution ensures quality gates, test-first approach, and complete context handoffs.

**HOW**: See pm-guide.md for phase execution, test-first guidance, progress tracking, and agent briefing. See worklog-format.md for WORKLOG entry formats. See development-loop.md for quality gates.

## Usage

```bash
/implement TASK-001 1.1    # Execute phase 1.1
/implement BUG-003 2.2     # Execute phase 2.2
/implement PROJ-123 1.1    # Execute Jira issue phase
/implement TASK-001 --next # Smart: find and execute next uncompleted phase
/implement --next          # Auto-detect current task, execute next phase
```

## Pre-Execution Context

**Load workflow rules** (read before proceeding):

```bash
Read: docs/development/guidelines/pm-guide.md
Read: docs/development/guidelines/worklog-format.md
Read: docs/development/guidelines/development-loop.md
Read: docs/development/guidelines/git-workflow.md
```

**pm-guide.md contains**:
- Phase structures and patterns
- Progress tracking rules
- Test-first protocol
- Agent context briefing patterns
- Security detection criteria

**worklog-format.md contains**:
- Standard WORKLOG format (HANDOFF and COMPLETE entries)
- Troubleshooting WORKLOG format (hypothesis-based)
- Entry timing and best practices

**development-loop.md contains**:
- Quality gate requirements and thresholds
- Code review process
- Agent coordination patterns

**This is the source of truth for HOW to execute. Command implements WHAT.**

## Execution Steps

### 1. Parse Parameters

- Parse issue ID and phase number
- Continue to step 2

**Smart next mode** (TASK-001 --next):
- Parse issue ID
- Read PLAN.md
- Find first uncompleted phase (unchecked checkbox)
- Show phase to user, ask confirmation
- Continue to step 2

**Auto next mode** (--next only):
- Detect current task from git branch or recent WORKLOG
- Read PLAN.md
- Find first uncompleted phase
- Show phase to user, ask confirmation
- Continue to step 2

### 2. Load Task Context

```bash
# Find and read task files
Glob: pm/issues/{ISSUE-ID}-*/
Read: PLAN.md (phase details, acceptance criteria)
Read: WORKLOG.md (previous work context)
Read: TASK.md or BUG.md (requirements)

# Load epic context if linked
Read: pm/epics/EPIC-###-*.md
```

### 3. Branch Management

**Branch creation**: See git-workflow.md for naming patterns.

```bash
# Check current branch
Bash: git branch --show-current

# Create feature branch if needed (per git-workflow.md)
# Pattern: feature/{ISSUE-ID} or bugfix/{ISSUE-ID}
```

### 4. Spawn Domain Agent

**Agent selection**: Based on phase type (frontend, backend, database, etc.)

**Context briefing**: See pm-guide.md "Agent Context Briefing" for filtering patterns.

Provide agent with:
- Phase requirements (filtered from PLAN.md)
- Relevant context only (not full PLAN.md)
- Architecture decisions (from architecture-overview.md)
- Design patterns (if frontend work)

**Test-first guidance**: See pm-guide.md "Test-First Guidance Protocol".

Spawn agent via Task tool:
```
Task(
  subagent_type: {domain-specialist},
  prompt: {filtered context + phase requirements}
)
```

### 5. Track Progress

**Update PLAN.md**:
- Mark phase checkbox as complete
- Add completion timestamp
- Update progress indicators

**Update WORKLOG.md**: See worklog-format.md for HANDOFF and COMPLETE entry formats.

Required entry format (per guideline):
- Timestamp (run `date '+%Y-%m-%d %H:%M'`)
- Phase completed
- What was done (brief)
- Gotchas/Lessons
- Files changed
- Handoff information (if applicable)

### 6. Quality Gates

**Per-phase validation**: See development-loop.md "Quality Gates" for thresholds.

Required checks:
- Tests pass (if test-first phase)
- Code review score ≥ 90 (invoke code-reviewer agent)
- Security approval (if security-relevant per pm-guide.md criteria)
- Documentation updated (if user-facing changes)

### 7. Post-Phase Review and Adaptation

**Conduct review cycle after quality gates pass**.

See development-loop.md "Review and Adapt Plans" for the complete 5-step process:
1. Run quality reviews (code/security/architecture)
2. Analyze findings for plan impacts
3. Adapt TASK/PLAN files if needed
4. Document changes in WORKLOG
5. Inform user of adaptations

**WORKLOG documentation**: Use "Review Cycle: Plan Updated" entry format from worklog-format.md if plans changed.

### 8. Phase Completion

Display:
- ✓ Phase {X.Y} completed
- Quality gate status
- Review findings summary (if any)
- Plan adaptations made (if any)
- Next phase available (if any)
- Suggested next action

## Agent Coordination

**Primary**: Domain specialists (backend-specialist, frontend-specialist, database-specialist, etc.) based on phase type

**Supporting**:
- test-engineer (test-first approach)
- code-reviewer (quality validation)

**Conditional**:
- security-auditor (automatic for security-relevant phases per pm-guide.md criteria)

**Coordination method**: WORKLOG.md handoff entries per worklog-format.md.

## Error Handling

- **Issue not found**: "Cannot find issue {ID} in pm/issues/"
- **PLAN.md missing**: "No PLAN.md found - run /plan {ID} first"
- **Phase not found**: "Phase {X.Y} not found in PLAN.md"
- **Phase already complete**: "Phase {X.Y} already completed. Use /implement {ID} {next-phase}"
- **Quality gate failure**: Loop with agent until gates pass

## Integration

**Workflow position**:
```
/plan TASK-### → /implement TASK-### 1.1 → /implement TASK-### 1.2 → ... → /commit
```

**Progress tracking**: Updates PLAN.md checkboxes and WORKLOG.md entries
**Next steps**: Continue with next phase or `/commit` when task complete

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
    [Executes phase per pm-guide.md]

    ✓ Phase 1.1 complete
    ✓ Updated PLAN.md checkbox
    ✓ Updated WORKLOG.md

    Next: /implement PROJ-123 1.2
```

### --next Flag Examples

**Example 1: Explicit task with --next**
```
User: /implement TASK-001 --next

AI: Checking TASK-001 plan...

    Next uncompleted phase: 1.2 - Implement authentication logic
    (Phase 1.1 completed previously)

    Proceed with Phase 1.2? (yes/no/specify)

User: yes

AI: Executing phase 1.2...
    [Standard workflow continues]
```

**Example 2: Auto-detect from branch**
```
User: /implement --next

AI: Detecting current task...
    ✓ Found from branch: feature/TASK-001

    Checking TASK-001 plan...
    Next uncompleted phase: 2.1 - Create database schema

    Proceed with Phase 2.1? (yes/no/specify)

User: yes

AI: Executing phase 2.1...
    [Standard workflow continues]
```

**Example 3: All phases complete**
```
User: /implement TASK-001 --next

AI: Checking TASK-001 plan...

    ✓ All phases completed!

    Task completion checklist:
    - [x] All PLAN.md phases complete
    - [ ] All acceptance criteria satisfied
    - [ ] Full test suite passing

    Would you like to run task completion validation?
```

**Example 4: User specifies different phase**
```
User: /implement TASK-001 --next

AI: Next uncompleted phase: 2.1 - Create database schema

    Proceed with Phase 2.1? (yes/no/specify)

User: specify 1.3

AI: Switching to Phase 1.3 - Write integration tests
    Executing phase 1.3...
    [Standard workflow continues]
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

**Following** `pm-guide.md` **progress tracking protocol**, after each phase:

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
- ✓ See `worklog-format.md` for HANDOFF and COMPLETE entry formats
- ✓ Prepend to top (reverse chronological)
- ✓ For complex decisions, use longer WORKLOG entries or create ADR via `/adr`

**pm/epics/EPIC-###-name.md** (if epic exists):
- ✓ Mark task complete when all phases done: `- [x] TASK-001`

### Validation Before Completion

**Following** `pm-guide.md` **task completion validation checklist**:
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
3. **Load context**: TASK.md/BUG.md or Jira, ADRs, WORKLOG, coding-standards.md
4. **Check test-first**: Follow `pm-guide.md` guidance protocol
5. **Brief agent**: Provide domain-filtered context per `pm-guide.md`
6. **Apply coding standards**: Agent reads `coding-standards.md` BEFORE writing code
7. **Execute phase**: Agent follows development loop with quality gates
8. **Validate standards**: Agent self-checks compliance before marking phase complete
9. **Update progress**: Follow `pm-guide.md` progress tracking protocol
10. **Check completion**: If all phases done, follow `pm-guide.md` task completion validation

### Implementation Agent Protocol

**Before writing code:**
1. Read `coding-standards.md` for:
   - File naming conventions (for new files)
   - Identifier naming patterns (variables, functions, classes, constants)
   - Import organization structure
   - Line length and indentation rules
   - Language-specific idioms
2. Extract machine-readable config from frontmatter (language, formatter, linter, etc.)
3. Note any project-specific conventions or examples

**While writing code:**
1. Apply standards proactively (don't wait for code review)
2. Follow file naming when creating new files
3. Organize imports according to defined order
4. Keep line length within configured limits
5. Use correct identifier naming patterns

**Before marking phase complete:**
1. Self-validate against coding standards:
   - [ ] File names match convention
   - [ ] Identifiers follow naming patterns
   - [ ] Imports organized correctly
   - [ ] Line length within limits
   - [ ] Indentation consistent
2. Auto-detectable violations BLOCK phase completion
3. Document rationale for necessary deviations in WORKLOG
4. Run formatter/linter if configured

**See**: `coding-standards.md` "Automated Quality Checks" section for complete validation checklist.

**Key Principle**: Commands orchestrate, guidelines configure. Workflow rules distributed across specialized guideline files for team customization.
