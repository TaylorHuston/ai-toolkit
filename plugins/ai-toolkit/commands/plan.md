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
/plan TASK-001    # Local: Creates pm/issues/TASK-001-*/PLAN.md
/plan BUG-003     # Local: Creates pm/issues/BUG-003-*/PLAN.md
/plan PROJ-123    # Jira: Fetches from Jira, creates PLAN.md locally
```

## Execution Flow

**Before you start**: Read pm-guide.md, architecture-overview.md, design-overview.md for complete planning methodology.

### High-Level Steps

1. **Locate & Load**
   - Parse issue ID (TASK/BUG/PROJ)
   - Read TASK.md/BUG.md or fetch from Jira
   - If TASK has parent spec: Read SPEC-###.md acceptance scenarios

2. **Validate Scoping**
   - Check if task is deployable unit (per pm-guide.md scoping principles)
   - Warn if underscoped (too small to deploy independently)
   - Get user confirmation if needed

3. **Deep Analysis**
   - Use sequential-thinking for problem analysis
   - Identify research needs (libraries, patterns, best practices)
   - Determine approach options and trade-offs

4. **Research**
   - Library docs via Context7 (latest patterns and examples)
   - Best practices via web search (recent guides, common pitfalls)
   - Synthesize with architecture and design guidelines

5. **Generate Phase Breakdown**
   - Create test-first phases (per pm-guide.md structure)
   - Strategic WHAT, not tactical HOW (agents decide implementation)
   - Map acceptance scenarios to test phases (if spec exists)
   - Apply complexity scoring (pm-guide.md)

6. **Mandatory Reviews**
   - code-architect: Architectural alignment (always required)
   - security-auditor: Security review (conditional per pm-guide.md)

7. **Present Plan**
   - Show phase breakdown
   - Scenario coverage mapping (if spec)
   - Research summary
   - Review signoffs
   - Complexity estimate

**See pm-guide.md "Planning Methodology" for complete step-by-step details.**

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

    Phase breakdown (test-first):
    1.1 Design authentication architecture
    1.2 Implement user model with secure password storage
    1.3 Create login endpoint with JWT generation
    1.4 Write integration tests (covers all 3 scenarios)

    Reviews:
    ✓ code-architect: Approved
    ✓ security-auditor: Approved (suggested httpOnly cookies)

    Complexity: 8 points (Medium)

    Next: /implement TASK-001 1.1
```

## Agent Coordination

**Primary**: project-manager (analysis), test-engineer (testing strategy)
**Mandatory**: code-architect (architectural review - always)
**Conditional**: security-auditor (security review - auto-detected per pm-guide.md)

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
