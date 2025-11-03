---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for individual tasks and bugs"
argument-hint: "TASK-### | BUG-### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/development-loop.md  # Complexity scoring, plan structure, code-architect review
---

# /plan Command

**Purpose**: Create `PLAN.md` file with phase-based breakdown in task/bug directory.

**IMPORTANT**: This command ONLY creates the PLAN.md file. It does NOT start implementation. Use `/implement` command separately when ready to begin work.

**Why separate file?** Keeps TASK.md clean for syncing with external PM tools (Jira, Linear, etc.) while AI manages implementation details in PLAN.md.

## Usage

```bash
/plan TASK-001    # Local: Creates pm/issues/TASK-001-*/PLAN.md
/plan BUG-003     # Local: Creates pm/issues/BUG-003-*/PLAN.md
/plan PROJ-123    # Jira: Fetches from Jira, creates PLAN.md locally
```

**Simple invocation**: Just pass the issue ID. Command automatically:
- Locates directory in `pm/issues/` or creates if Jira issue
- Reads requirements (from TASK.md/BUG.md or Jira)
- Loads epic context and ADRs
- Creates `PLAN.md` with phase-based breakdown
- **Invokes code-architect for mandatory review before presenting to user**

## Agent Coordination

**Primary**: project-manager (task analysis and planning), test-engineer (testing strategy)
**Supporting**: Domain specialists (frontend-specialist, backend-specialist, database-specialist) for complexity assessment
**Mandatory Review**: code-architect (architectural signoff), security-auditor (conditional - if security-relevant)

## How It Works

### 1. Read Configuration

**All planning rules are defined in** `docs/development/guidelines/development-loop.md`:
- **Complexity Scoring**: Point values, decomposition thresholds (customizable)
- **Plan Structure**: Phase templates by task type (frontend, backend, bug fixes, etc.)
- **Test-First Patterns**: TDD, BDD, Test Pyramid, Pragmatic approaches
- **Code-Architect Review**: Mandatory review requirements

**The command reads this configuration and adapts behavior to your project's settings.**

### 2. Load Context

**For Local Issues (TASK-###, BUG-###)**:
- Find directory: `pm/issues/TASK-###-*/` or `pm/issues/BUG-###-*/`
- Read TASK.md or BUG.md for requirements and acceptance criteria
- Load epic context from `epic:` field in frontmatter (if present)

**For Jira Issues (PROJ-###)**:
- Check CLAUDE.md: Verify `jira.enabled: true`
- Check MCP: Ensure Atlassian MCP tools available
- Fetch from Jira: Get description, acceptance criteria, issue type
- Create directory: `pm/issues/PROJ-###-{name}/` if doesn't exist
- No TASK.md created (Jira is source of truth)

**Common Context**:
- Read relevant ADRs from `docs/project/adrs/`
- Check for existing PLAN.md (update workflow)

### 3. Analyze Complexity

**Following** `development-loop.md` **complexity scoring configuration**:
- Calculate complexity score from indicators (multi-domain, security, database, etc.)
- Compare to thresholds (high ≥5 points by default)
- Recommend decomposition if high complexity
- Teams can customize scoring rules in guideline

### 4. Generate Plan

**Following** `development-loop.md` **plan structure templates**:
- Select phase structure based on task type (frontend, backend, bug fix, database, security)
- Create logical phases with checkboxed items
- Include test-first patterns from guideline configuration
- Add complexity score to PLAN.md frontmatter

### 5. Code-Architect Review (MANDATORY)

**Following** `development-loop.md` **plan review requirements**:
- **Use Task tool** to invoke code-architect agent
- Provide full context: requirements, generated plan, complexity analysis
- Code-architect reviews: architectural soundness, ADR consistency, phase structure, cross-cutting concerns
- Incorporate feedback and get explicit approval

### 6. Security-Auditor Review (CONDITIONAL)

**Following** `development-loop.md` **security detection criteria**:
- **Auto-detect** if task is security-relevant (keywords, file patterns)
- **If security-relevant**: Use Task tool to invoke security-auditor agent
- Provide context: requirements, plan, security concerns
- Security-auditor reviews: threat modeling, input validation, crypto usage, authorization logic, OWASP compliance
- Incorporate feedback and get explicit approval
- **May require**: Additional security phases (threat modeling, penetration testing)
- **Only after BOTH reviews** (code-architect + security-auditor if needed): Present plan to user

### 7. Present to User

**After all required reviews** (code-architect + security-auditor if applicable):
- Show generated PLAN.md
- Explain complexity score and decomposition recommendation (if applicable)
- Note if security-auditor reviewed and approved (for security-relevant tasks)
- Ask for modifications or approval

**STOP HERE**: Do not proceed to implementation without explicit user instruction.

## Jira Integration

**Hybrid Mode**: Works with both local (TASK-###, BUG-###) and Jira (PROJ-###) issues.

### ID Detection
- Local: `TASK-###`, `BUG-###` (e.g., TASK-001, BUG-042)
- Jira: `[A-Z]+-###` (e.g., PROJ-123, ENG-456)

### Jira Workflow
```
User: /plan PROJ-123

AI: Fetching PROJ-123 from Jira...
    Issue: User Registration Form
    Type: Story
    Status: To Do

    Analyzing complexity...
    Score: 3 (Medium - multi-domain integration)
    Security-relevant: Yes (detected keywords: auth, login)

    Generating implementation plan...
    Invoking code-architect for review...
    ✓ Code-architect approved plan

    Invoking security-auditor for security review...
    ✓ Security-auditor approved (recommended adding threat modeling phase)

    ✓ Created pm/issues/PROJ-123-user-registration/PLAN.md

    [Shows plan to user]

    Ready for: /implement PROJ-123 1.1
```

### Error Handling

**Issue not found**:
```
Error: PROJ-123 not found in Jira.
Check: https://company.atlassian.net/browse/PROJ-123
```

**MCP unavailable**:
```
Error: Jira integration enabled but Atlassian MCP not available.
Options:
1. Configure Atlassian Remote MCP Server
2. Disable Jira in CLAUDE.md
3. Use local issue: /plan TASK-001
```

## Outputs

**pm/issues/ISSUE-ID-name/PLAN.md**:
- YAML frontmatter (metadata, complexity score, Jira info if applicable)
- Phase headers with logical grouping
- Checkboxed implementation steps
- Test-first patterns embedded (per guideline configuration)
- Code-architect approved

**File structure**:
```
pm/issues/TASK-001-user-registration/
├── TASK.md          # Requirements (local) or fetched from Jira
├── PLAN.md          # AI-managed implementation plan (this command creates)
├── WORKLOG.md       # Implementation history (created by /implement)
└── RESEARCH.md      # Technical decisions (created as needed)
```

## Integration with Workflow

**Position**: After task creation, before /implement

- **After task creation**: Takes requirements as input
- **After /adr**: Incorporates technical decisions
- **Before /implement**: Provides implementation roadmap
- **Complexity-aware**: Suggests decomposition when needed

**Clear separation**: `/plan` creates the plan, `/implement` executes it.

## Implementation Notes

When implementing `/plan ISSUE-ID`:

1. **Detect ID type**: Local (TASK-###/BUG-###) vs Jira ([A-Z]+-###)

2. **Load context**: Read TASK.md/BUG.md or fetch from Jira

3. **Analyze complexity**: Following `development-loop.md` scoring rules

4. **Generate plan**: Following `development-loop.md` phase templates

5. **Code-architect review (MANDATORY)**:
   - Use Task tool to invoke code-architect
   - Provide requirements + generated plan
   - Incorporate feedback
   - Get approval

6. **Security-auditor review (CONDITIONAL)**:
   - Auto-detect if security-relevant (keywords, file patterns)
   - If yes: Use Task tool to invoke security-auditor
   - Provide requirements + plan + security concerns
   - Incorporate feedback
   - Get approval

7. **Present to user**: Show plan after all required reviews

8. **STOP**: Do not implement without explicit `/implement` command

**Key Principle**: Commands orchestrate, guidelines configure. All planning rules live in `development-loop.md` for easy customization.
