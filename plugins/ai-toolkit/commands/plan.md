---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for individual tasks and bugs"
argument-hint: "TASK-### | BUG-### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs", "WebSearch", "WebFetch"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/pm-guide.md  # Plan execution methodology, complexity scoring, test-first patterns
---

# /plan Command

**WHAT**: Create comprehensive PLAN.md with phase-based breakdown through deep analysis and research.

**WHY**: Planning is critical—spending 3-5 minutes upfront prevents costly mistakes during implementation.

**HOW**: See pm-guide.md for complexity scoring, plan structure, test-first patterns, and review requirements.

## Usage

```bash
/plan TASK-001    # Local: Creates pm/issues/TASK-001-*/PLAN.md
/plan BUG-003     # Local: Creates pm/issues/BUG-003-*/PLAN.md
/plan PROJ-123    # Jira: Fetches from Jira, creates PLAN.md locally
```

## Pre-Execution Context

**Load project context** (read before proceeding):

```bash
Read: CLAUDE.md
Read: docs/development/guidelines/pm-guide.md
Read: docs/project/architecture-overview.md
Read: docs/project/design-overview.md
```

## Execution Steps

### 1. Locate Issue and Load Context

```bash
# Parse issue ID from arguments
# If local (TASK/BUG): Glob pm/issues/{ISSUE-ID}-*/
# If Jira (PROJ): Fetch via MCP, create local directory
Read: TASK.md/BUG.md or Jira description

# If TASK.md has `spec: SPEC-###` in frontmatter:
#   Read parent spec: pm/specs/SPEC-###-*.md
#   Extract: Acceptance Scenarios section
#   Note: Which scenarios are relevant to this task?
```

**Output**: Task requirements + relevant feature scenarios (if spec exists)

### 2. Deep Thinking Phase

```bash
# Use sequential-thinking tool
Analyze:
- Problem understanding
- Technical approach options
- Research needs (libraries, patterns, best practices)
- Acceptance scenarios from parent spec (if available)
  - How will we validate each scenario?
  - What test setup is needed?
  - Are there edge cases beyond the scenarios?
```

**Output**: Research requirements, approach direction, and scenario coverage strategy.

### 3. Library Research

```bash
# For each library/framework mentioned:
Resolve library ID via Context7
Fetch latest documentation
Extract relevant patterns and examples
```

**Output**: Library-specific implementation guidance.

### 4. Best Practices Research

```bash
# Web search for:
Recent guides (2024-2025)
Common patterns
Known pitfalls
```

**Output**: Current best practices and anti-patterns.

### 5. Synthesize Context

Combine:
- Deep thinking insights
- Library documentation findings
- Web research best practices
- Architecture overview decisions
- Design patterns (from design-overview.md)

### 6. Generate Phase Breakdown

**Complexity scoring**: See pm-guide.md for point values and thresholds.

```bash
Write: pm/issues/{ISSUE-ID}-*/PLAN.md
```

Structure per pm-guide.md:
- Phase breakdown (test-first)
  - If parent spec has acceptance scenarios: generate test phases covering scenarios
  - If no parent spec: generate tests from TASK.md acceptance criteria
- Scenario Coverage section (if spec exists)
  - Map which phases validate which scenarios
  - Explicit traceability from spec → plan → tests
- Acceptance criteria checkboxes
- Complexity estimates

### 7. Mandatory Reviews

**Code-architect review** (always required):
```bash
Task: Spawn code-architect agent
# Agent reviews architectural alignment
# Provides signoff or revision requests
```

**Security-auditor review** (conditional):
```bash
# Detection criteria: See pm-guide.md "Security Detection"
# If security-relevant: Spawn security-auditor agent
# Reviews security implications
```

### 8. Present Plan

Display:
- Phase breakdown
- Scenario coverage mapping (if spec exists)
  - Show which scenarios are validated by which phases
  - Highlight any uncovered scenarios
- Research summary (libraries used, key findings)
- Review signoffs
- Estimated complexity

## Agent Coordination

**Primary**: project-manager (analysis), test-engineer (testing strategy)
**Mandatory**: code-architect (architectural review)
**Conditional**: security-auditor (if security-relevant per pm-guide.md)

## Error Handling

- **Issue not found**: "Issue {ID} not found in pm/issues/"
- **PLAN.md exists**: Ask to overwrite or append
- **Jira fetch fails**: "Cannot fetch PROJ-{ID} - check MCP"
- **Context files missing**: Warning but continue (use defaults)

## Example Output

```
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

## Integration

```
/spec → /plan TASK-### → /implement TASK-### 1.1
```

**Creates**: `pm/issues/{ISSUE-ID}-*/PLAN.md`
**Next step**: `/implement {ISSUE-ID} 1.1`
