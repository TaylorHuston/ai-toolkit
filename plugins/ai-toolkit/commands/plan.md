---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for individual tasks and bugs"
argument-hint: "TASK-### | BUG-### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs", "WebSearch", "WebFetch"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # Plan structure, complexity scoring, scoping principles, review requirements
  - docs/development/templates/architecture-overview-template.md  # Architecture overview template for reference
  - docs/development/templates/design-overview-template.md  # Design overview template for reference
---

# /plan Command

**WHAT**: Create comprehensive PLAN.md with phase-based breakdown through deep analysis and research.

**WHY**: Planning is critical—spending 3-5 minutes upfront prevents costly mistakes during implementation.

**HOW**: See pm-guide.md for plan structure, complexity scoring, scoping validation, strategic vs tactical planning, and mandatory review requirements.

## Usage

```bash
/plan TASK-001       # Local: Creates pm/issues/TASK-001-*/PLAN.md
/plan BUG-003        # Local: Creates pm/issues/BUG-003-*/PLAN.md
/plan PROJ-123       # Jira: Fetches from Jira, creates PLAN.md locally

# Spike exploration plans
/plan --spike SPIKE-001  # Creates multiple PLAN-N.md files for each approach
```

## Execution Flow

**Before you start**: Read pm-guide.md, architecture-overview.md, design-overview.md for complete planning methodology.

### High-Level Steps

1. **Locate & Load**
   - Parse issue ID (TASK/BUG/PROJ)
   - Read TASK.md/BUG.md or fetch from Jira
   - If TASK has parent spec: Read SPEC-###.md acceptance scenarios

2. **Find Similar Completed Tasks**
   - Glob: `pm/issues/*/WORKLOG.md` to find completed work
   - Grep for keywords from current task description
   - Read 2-3 most relevant WORKLOGs
   - Extract: patterns used, gotchas encountered, test counts
   - Note: This informs deep analysis with real-world context

3. **Deep Analysis**
   - Use sequential-thinking for problem analysis (informed by similar work)
   - Identify research needs (libraries, patterns, best practices)
   - Determine approach options and trade-offs
   - Consider how this task compares to similar completed tasks

4. **Validate Scoping**
   - Check if task is deployable unit (per pm-guide.md scoping principles)
   - Warn if underscoped (too small to deploy independently)
   - Get user confirmation if needed

5. **Research**
   - Library docs via Context7 (latest patterns and examples)
   - Best practices via web search (recent guides, common pitfalls)
   - Synthesize with architecture and design guidelines

6. **Generate Phase Breakdown**
   - **Choose structure based on task type:**
     - **TDD Phases** (feature work): Use X.RED/X.GREEN/X.REFACTOR sub-phases
     - **Simple Phases** (infra/scaffolding): No TDD structure needed
   - **Phase = One Behavior = One RED/GREEN/REFACTOR cycle**
   - Strategic WHAT with tactical specificity (see pm-guide.md "Strategic vs Tactical - The Nuance")
   - Map acceptance scenarios to phases with explanations (if spec exists)
   - Apply complexity scoring with test count estimates (pm-guide.md)
   - Create comparative context section (vs similar tasks)
   - Add domain/business context if relevant

7. **Mandatory Reviews**
   - code-architect: Architectural alignment (always required)
   - security-auditor: Security review (conditional per pm-guide.md)

8. **Present Plan**
   - Show phase breakdown (3-level hierarchy)
   - Comparative context (vs similar tasks)
   - Domain/business context (if relevant)
   - Scenario coverage mapping with explanations (if spec)
   - Test count estimates
   - Research summary
   - Review signoffs
   - Complexity estimate

**See pm-guide.md "Planning Methodology" for complete step-by-step details.**

## Spike Planning (--spike flag)

**When to use:** `/plan --spike SPIKE-001` for creating exploration plans when technical approach is uncertain.

**Behavior with --spike flag:**

1. **Read SPIKE.md**
   - Load questions to answer
   - Load approaches to explore (numbered)
   - AI suggests number of plans based on approaches defined

2. **Create Multiple PLAN-N.md Files**
   - Creates numbered exploration plans: PLAN-1.md, PLAN-2.md, etc.
   - Each plan explores a different technical approach
   - Example: PLAN-1.md (GraphQL), PLAN-2.md (REST)

3. **PLAN-N.md Structure**
   - Adds spike reminder at top:
   ```markdown
   > **⚠️ SPIKE EXPLORATION**
   > This is exploratory work. Code will NOT be committed to production.
   > Use `/implement --spike SPIKE-001 --plan 1` to execute.
   ```
   - Phases focus on answering questions, not building features
   - Success criteria = questions answered, not features delivered

4. **Interactive Planning**
   - AI: "SPIKE.md defines 2 approaches. Create 2 exploration plans?"
   - For each approach: Generate phases to explore that approach
   - Phases: Setup → Prototype → Benchmark → Evaluate

**Example:**
```bash
/plan --spike SPIKE-001
# AI reads SPIKE-001.md
# AI: "I see 2 approaches: GraphQL and REST. Create 2 exploration plans?"
# Creates PLAN-1.md (GraphQL exploration phases)
# Creates PLAN-2.md (REST exploration phases)
```

**Differences from regular planning:**
- Multiple PLAN-N.md files (not single PLAN.md)
- Exploration phases (not implementation phases)
- No test-first enforcement (exploratory code)
- No complexity scoring (time-boxed exploration)
- No mandatory reviews (throwaway prototypes)

**See:** spike-workflow.md for complete spike planning workflows and templates.

## Strategic vs Tactical Planning

**CRITICAL**: Plans describe **WHAT** to build (objectives), NOT **HOW** to build it.

✅ **Strategic** (correct):
- "1.2 Implement user authentication with password hashing"
- "2.1 Create REST API for user management"

❌ **Tactical** (wrong):
- "1.2 Create User class with bcrypt.hash() using 10 salt rounds"
- "2.1 Use Express Router with /api/users endpoint, body-parser middleware"

**Why strategic:**
- Agents adapt to current codebase state
- WORKLOG provides real-time context and lessons learned
- Flexibility for better approaches discovered during work

See pm-guide.md "Strategic vs Tactical Planning" for complete guidance.

## Example

```
User: /plan TASK-001

AI: Creating plan for TASK-001: User Login Flow
    ✓ Loaded parent spec SPEC-001 (3 acceptance scenarios)
    ✓ Research: JWT patterns, security best practices
    ✓ Task has testable behavior → Using TDD phase structure

    Phase breakdown (TDD: RED/GREEN/REFACTOR per behavior):

    ### Phase 1 - User can log in with valid credentials
    #### 1.RED - Write Failing Tests
    - [ ] 1.1 Write login success tests
    - [ ] 1.2 [CHECKPOINT] Verify tests FAIL
    #### 1.GREEN - Implement to Pass
    - [ ] 1.3 Implement login endpoint with JWT
    - [ ] 1.4 [CHECKPOINT] Verify tests PASS
    #### 1.REFACTOR - Clean Up
    - [ ] 1.5 Refactor, review >= 90, commit

    ### Phase 2 - Invalid credentials return error
    #### 2.RED - Write Failing Tests
    - [ ] 2.1 Write invalid password tests
    - [ ] 2.2 [CHECKPOINT] Verify tests FAIL
    #### 2.GREEN - Implement to Pass
    - [ ] 2.3 Add credential validation
    - [ ] 2.4 [CHECKPOINT] Verify tests PASS
    #### 2.REFACTOR - Clean Up
    - [ ] 2.5 Refactor, review >= 90, commit

    Reviews:
    ✓ code-architect: Approved
    ✓ security-auditor: Approved (suggested httpOnly cookies)

    Complexity: 8 points (Medium)

    Next: /implement TASK-001 1.RED
```

## Error Handling

- **Issue not found**: Check `pm/issues/{ID}-*/` exists
- **PLAN.md exists**: Ask to overwrite or create new version
- **Jira unavailable**: Check MCP connection and CLAUDE.md config
- **Context files missing**: Warning but proceed with defaults

## Integration

**Workflow position**:
```
/spec SPEC-### → /plan TASK-### → /implement TASK-### 1
```

**Creates**: `pm/issues/{ISSUE-ID}-*/PLAN.md`
**Next step**: `/implement {ISSUE-ID} 1` to start execution

See pm-guide.md for complete workflow integration.
