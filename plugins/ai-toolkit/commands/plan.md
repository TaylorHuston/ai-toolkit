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

### 0. MANDATORY Context Review (FIRST STEP)

**BEFORE doing ANYTHING else, you MUST read these files to refresh your context:**

**Project Configuration & Standards:**
1. **`CLAUDE.md`** (root) - Project configuration, tech stack, team standards, workflow customization
2. **`docs/development/guidelines/`** - ALL guideline files in this directory:
   - `development-loop.md` - Complexity scoring, plan structure, test-first patterns, quality gates
   - `git-workflow.md` - Branch naming, commit conventions (if referenced in plans)
   - `testing-strategy.md` - Test patterns, coverage requirements (if exists)
   - `architecture-guidelines.md` - Architectural standards (if exists)
   - ANY other guideline files present - all provide project-specific context

**Project Knowledge Base:**
3. **`docs/project/architecture-overview.md`** - Approved architectural decisions, system design, technical patterns
4. **`docs/project/design-overview.md`** - Approved UI/UX designs, design patterns, component specifications

**Why this is CRITICAL:**
- **CLAUDE.md** defines your tech stack, APIs, deployment setup, testing approach, team conventions
- **Guidelines** contain project-specific rules that override any defaults you might assume
- **Architecture overview** shows existing decisions you must align with (don't contradict approved ADRs)
- **Design overview** shows approved UI patterns you should reference and follow
- **Without this context**, you'll create plans that:
  - Contradict existing architecture decisions
  - Ignore established design patterns
  - Use wrong complexity scoring rules
  - Miss project-specific test requirements
  - Violate team workflow conventions

**Failure to read these files will result in:**
- Plans that contradict architecture-overview.md decisions
- Missing references to relevant ADRs
- Wrong complexity thresholds (each project customizes this)
- Ignoring approved design patterns from design-overview.md
- Plans incompatible with the actual tech stack in CLAUDE.md
- Using generic patterns instead of project-specific guidelines

### 1. Read Configuration

**After completing the mandatory context review above**, read task-specific configuration:

**All planning rules are defined in** `docs/development/guidelines/development-loop.md`:
- **Complexity Scoring**: Point values, decomposition thresholds (customizable)
- **Plan Structure**: Phase templates by task type (frontend, backend, bug fixes, etc.)
- **Test-First Patterns**: TDD, BDD, Test Pyramid, Pragmatic approaches
- **Code-Architect Review**: Mandatory review requirements

**The command reads this configuration and adapts behavior to your project's settings.**

### 2. Load Task Context

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

**CRITICAL**: **Use `pm/templates/plan.md` as the structure template**:
- Read `pm/templates/plan.md` to understand the expected format
- Keep plan CONCISE - phases should be simple checklist format, not verbose documentation
- Avoid excessive detail - phases are suggestions that can be modified during implementation
- **BAD**: Long paragraphs, detailed explanations, code examples, extensive documentation within phases
- **GOOD**: Clear phase headers with numbered checklist items (1.1, 1.2, etc.)

**Following** `development-loop.md` **plan structure templates**:
- Select phase structure based on task type (frontend, backend, bug fix, database, security)
- Create logical phases with checkboxed items (concise format)
- Include test-first patterns from guideline configuration
- Add complexity score to PLAN.md frontmatter

**Template structure (from pm/templates/plan.md)**:
```markdown
# Implementation Plan: TASK-001 Task Name

Brief overview (1-2 sentences)

## Phases

### Phase 1 - Phase Name
- [ ] 1.1 Step description
  - [ ] 1.1.1 Sub-step if needed
- [ ] 1.2 Step description

### Phase 2 - Phase Name
- [ ] 2.1 Step description
- [ ] 2.2 Step description

---

**Alternative Patterns**: TDD, BDD, Test Pyramid, Pragmatic

## Complexity Analysis
Score: X/10
Indicators: ...
Recommendation: ...
```

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
- **Format**: Follows `pm/templates/plan.md` structure (CONCISE)
- YAML frontmatter (metadata, complexity score, Jira info if applicable)
- Brief overview (1-2 sentences)
- Phase headers with numbered checklist items (1.1, 1.2, etc.)
- **No verbose documentation** - phases are simple checklists, not detailed guides
- Test-first patterns noted at bottom (Alternative Patterns section)
- Complexity analysis at end
- Code-architect approved

**Key point**: PLAN.md is a concise roadmap, not comprehensive documentation. Details are worked out during `/implement`.

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

**0. MANDATORY CONTEXT REVIEW (DO THIS FIRST)**:
   - **Read CLAUDE.md** (root) - Tech stack, APIs, team conventions, workflow config
   - **Read ALL guideline files** in `docs/development/guidelines/`:
     - `development-loop.md` - REQUIRED (complexity, plan structure, test patterns)
     - `git-workflow.md` - Branch naming, commit conventions
     - `testing-strategy.md` - Test patterns (if exists)
     - `architecture-guidelines.md` - Architectural standards (if exists)
     - Any other guideline files present
   - **Read `docs/project/architecture-overview.md`** - Approved architectural decisions, must align with these
   - **Read `docs/project/design-overview.md`** - Approved UI/UX patterns, reference these in plans
   - **DO NOT SKIP THIS STEP** - Without this context, your plan will be wrong

1. **Detect ID type**: Local (TASK-###/BUG-###) vs Jira ([A-Z]+-###)

2. **Load task context**: Read TASK.md/BUG.md or fetch from Jira

3. **Read template**: Read `pm/templates/plan.md` to understand the expected format and structure

4. **Analyze complexity**: Following `development-loop.md` scoring rules (that you just read in step 0)

5. **Generate plan**: Following `pm/templates/plan.md` structure (CONCISE format)
   - **Keep it simple**: Phases with numbered checklist items only
   - **No verbose documentation**: Avoid long paragraphs, code examples, or extensive explanations
   - **Phases are suggestions**: They can be modified during implementation
   - **Example good phase**:
     ```markdown
     ### Phase 1 - Setup
     - [ ] 1.1 Initialize project structure
     - [ ] 1.2 Configure dependencies
     - [ ] 1.3 Write setup tests
     ```
   - **Example bad phase** (too verbose):
     ```markdown
     ### Phase 1 - Setup
     - [ ] Initialize project structure
       - Create the following directories: src/, test/, config/
       - Set up package.json with these exact dependencies: ...
       - Configure webpack with the following options: ...
       (Pages of detailed explanations and code examples)
     ```

6. **Code-architect review (MANDATORY)**:
   - Use Task tool to invoke code-architect
   - Provide requirements + generated plan
   - Incorporate feedback
   - Get approval

7. **Security-auditor review (CONDITIONAL)**:
   - Auto-detect if security-relevant (keywords, file patterns)
   - If yes: Use Task tool to invoke security-auditor
   - Provide requirements + plan + security concerns
   - Incorporate feedback
   - Get approval

8. **Present to user**: Show plan after all required reviews

9. **STOP**: Do not implement without explicit `/implement` command

**Key Principle**: Commands orchestrate, guidelines configure. All planning rules live in `development-loop.md` for easy customization.
