---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for individual tasks and bugs"
argument-hint: "TASK-### | BUG-### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs", "WebSearch", "WebFetch"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/development-loop.md  # Complexity scoring, plan structure, code-architect review
---

# /plan Command

**Purpose**: Create comprehensive `PLAN.md` file with phase-based breakdown through deep analysis and research.

**IMPORTANT**:
- This command is **thorough and takes time** - expect 3-5 minutes for research and planning
- Uses **deep thinking**, **library research** (Context7), and **web research** for best practices
- Creates the PLAN.md file but does NOT start implementation
- Use `/implement` command separately when ready to begin work

**Why separate file?** Keeps TASK.md clean for syncing with external PM tools (Jira, Linear, etc.) while AI manages implementation details in PLAN.md.

**Why thorough?** Planning is critical - spending extra time upfront prevents costly mistakes during implementation.

## Usage

```bash
/plan TASK-001    # Local: Creates pm/issues/TASK-001-*/PLAN.md
/plan BUG-003     # Local: Creates pm/issues/BUG-003-*/PLAN.md
/plan PROJ-123    # Jira: Fetches from Jira, creates PLAN.md locally
```

**Simple invocation**: Just pass the issue ID. Command automatically:
- Locates directory in `pm/issues/` or creates if Jira issue
- Reads requirements (from TASK.md/BUG.md or Jira)
- **Uses deep thinking** to analyze problem and identify research needs
- **Researches libraries** via Context7 for latest documentation
- **Searches web** for best practices, guides, and examples
- Loads epic context and ADRs
- Synthesizes research findings with project context
- Creates comprehensive `PLAN.md` with phase-based breakdown
- **Invokes code-architect for mandatory review** before presenting to user
- **Invokes security-auditor if security-relevant** (auto-detected)

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

### 3. Deep Thinking Phase (MANDATORY)

**CRITICAL**: Use sequential thinking tool to deeply analyze the task BEFORE planning.

**Think through systematically**:

1. **Understanding the Problem**:
   - What is the core problem we're solving?
   - Who are the users and what do they need?
   - What's the business value?
   - What constraints exist (technical, timeline, scope)?

2. **Technical Analysis**:
   - What technologies/libraries are needed?
   - What architectural patterns should we use?
   - What are the integration points?
   - What could go wrong?

3. **Approach Exploration**:
   - What are different ways to solve this?
   - What are the tradeoffs of each approach?
   - Which approach aligns best with our architecture?
   - What prior art exists?

4. **Research Questions**:
   - What documentation do we need to consult?
   - What libraries/frameworks are involved?
   - What best practices should we follow?
   - What examples can guide us?

**Output**: Clear understanding of the problem, potential approaches, and research needed.

### 4. Research Phase - Context7 (Library Documentation)

**For each library/framework mentioned in the task**:

1. **Identify libraries**: Extract library names from requirements, tech stack, or task description
2. **Resolve library IDs**: Use Context7 to find official documentation
3. **Get latest docs**: Fetch up-to-date documentation focusing on:
   - Installation and setup
   - Core concepts and patterns
   - API reference for needed features
   - Best practices and examples
   - Common pitfalls

**Example**:
```
Task mentions "Add Next.js middleware for rate limiting"
→ Search Context7 for: Next.js (middleware patterns)
→ Search Context7 for: Rate limiting libraries (if using one)
→ Get latest docs on middleware, request/response handling
```

### 5. Research Phase - WebSearch (Best Practices & Examples)

**Search for recent guides, articles, and examples**:

1. **Best practices**: Search for "[technology] best practices 2025"
2. **Implementation guides**: Search for "[specific feature] implementation guide"
3. **Real-world examples**: Search for "[feature] examples" or "how to build [feature]"
4. **Common patterns**: Search for architectural patterns, design patterns
5. **Gotchas**: Search for "[technology] common mistakes" or "pitfalls"

**Focus on**:
- Recent content (prefer 2024-2025)
- Official guides and documentation
- Well-regarded tutorials and articles
- Stack Overflow discussions for common issues
- GitHub examples and reference implementations

**Time investment**: Spend 2-3 minutes per major technology/concept researching thoroughly.

### 6. Synthesize Research

**Combine findings**:
- Official docs from Context7
- Best practices from web search
- Examples and patterns discovered
- Project-specific context (ADRs, guidelines)

**Validate approach**:
- Does this align with architecture-overview.md?
- Does this follow design-overview.md patterns?
- Does this match development-loop.md standards?
- Are we using current best practices?

### 7. Analyze Complexity

**Following** `development-loop.md` **complexity scoring configuration**:
- Calculate complexity score from indicators (multi-domain, security, database, etc.)
- Compare to thresholds (high ≥5 points by default)
- Recommend decomposition if high complexity
- Teams can customize scoring rules in guideline

### 8. Generate Plan

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

## Research Summary
Libraries researched: [list]
Key findings: [bullet points]
Best practices applied: [bullet points]
```

### 9. Code-Architect Review (MANDATORY)

**Following** `development-loop.md` **plan review requirements**:
- **Use Task tool** to invoke code-architect agent
- Provide full context: requirements, generated plan, complexity analysis, research findings
- Code-architect reviews: architectural soundness, ADR consistency, phase structure, cross-cutting concerns
- Incorporate feedback and get explicit approval

### 10. Security-Auditor Review (CONDITIONAL)

**Following** `development-loop.md` **security detection criteria**:
- **Auto-detect** if task is security-relevant (keywords, file patterns)
- **If security-relevant**: Use Task tool to invoke security-auditor agent
- Provide context: requirements, plan, security concerns, research findings
- Security-auditor reviews: threat modeling, input validation, crypto usage, authorization logic, OWASP compliance
- Incorporate feedback and get explicit approval
- **May require**: Additional security phases (threat modeling, penetration testing)
- **Only after BOTH reviews** (code-architect + security-auditor if needed): Present plan to user

### 11. Present to User

**After all required reviews** (code-architect + security-auditor if applicable):
- Show generated PLAN.md with research summary
- Explain complexity score and decomposition recommendation (if applicable)
- Note if security-auditor reviewed and approved (for security-relevant tasks)
- Highlight key research findings that influenced the plan
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

    Using deep thinking to analyze task...
    [Sequential thinking output visible to user]
    ✓ Problem understood, research questions identified

    Researching libraries and documentation...
    → Context7: Next.js (form handling, validation)
    → Context7: React Hook Form (latest patterns)
    → Web: "Next.js form validation best practices 2025"
    → Web: "React authentication form examples"
    ✓ Research complete (4 sources)

    Synthesizing findings...
    - Using React Hook Form with Zod validation (current best practice)
    - Server actions for form submission (Next.js 14 pattern)
    - Secure password hashing with bcrypt (12 rounds minimum)

    Analyzing complexity...
    Score: 4 (Medium - multi-domain, security, validation)
    Security-relevant: Yes (detected keywords: auth, password, registration)

    Generating implementation plan...
    Invoking code-architect for review...
    ✓ Code-architect approved plan

    Invoking security-auditor for security review...
    ✓ Security-auditor approved (recommended adding threat modeling phase)

    ✓ Created pm/issues/PROJ-123-user-registration/PLAN.md

    [Shows plan to user with research summary]

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
- **Research summary** - libraries researched, key findings, best practices applied
- Code-architect approved (with security-auditor if applicable)

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

3. **Deep thinking phase (MANDATORY)**:
   - Use sequential thinking tool to analyze the problem
   - Understand user needs, technical constraints, approach options
   - Identify research questions and libraries needed
   - **Do not skip** - this is critical for thorough planning

4. **Research phase - Context7 (Library Documentation)**:
   - Identify all libraries/frameworks mentioned in task or tech stack
   - Use Context7 MCP to fetch latest official documentation
   - Focus on: setup, patterns, API reference, best practices
   - **Time investment**: Spend 1-2 minutes per major library

5. **Research phase - WebSearch (Best Practices & Examples)**:
   - Search for "[technology] best practices 2025"
   - Search for "[feature] implementation guide"
   - Search for real-world examples and patterns
   - **Time investment**: 2-3 minutes total for web research
   - **Prefer recent**: 2024-2025 content over older articles

6. **Synthesize research**:
   - Combine Context7 docs + web research + project guidelines
   - Validate approach against architecture-overview.md and ADRs
   - Ensure alignment with design-overview.md patterns

7. **Read template**: Read `pm/templates/plan.md` to understand the expected format and structure

8. **Analyze complexity**: Following `development-loop.md` scoring rules (that you just read in step 0)

9. **Generate plan**: Following `pm/templates/plan.md` structure (CONCISE format)
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

10. **Code-architect review (MANDATORY)**:
   - Use Task tool to invoke code-architect
   - Provide requirements + generated plan
   - Incorporate feedback
   - Get approval

11. **Security-auditor review (CONDITIONAL)**:
   - Auto-detect if security-relevant (keywords, file patterns)
   - If yes: Use Task tool to invoke security-auditor
   - Provide requirements + plan + security concerns
   - Incorporate feedback
   - Get approval

12. **Present to user**: Show plan after all required reviews

13. **STOP**: Do not implement without explicit `/implement` command

**Key Principle**: Commands orchestrate, guidelines configure. All planning rules live in `development-loop.md` for easy customization.
