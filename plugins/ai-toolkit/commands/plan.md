---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for individual tasks and bugs"
argument-hint: "TASK-### | BUG-### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs", "WebSearch", "WebFetch"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/plan-structure.md  # Complexity scoring, plan structure, code-architect review
---

# /plan Command

**WHAT**: Create comprehensive PLAN.md with phase-based breakdown through deep analysis and research.

**WHY**: Planning is critical—spending 3-5 minutes upfront prevents costly mistakes during implementation.

**HOW**: See plan-structure.md for complexity scoring, plan structure, test-first patterns, and review requirements.

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
Read: docs/development/guidelines/plan-structure.md
Read: docs/project/architecture-overview.md
Read: docs/project/design-overview.md
```

## Execution Steps

### 1. Locate Issue

```bash
# Parse issue ID from arguments
# If local (TASK/BUG): Glob pm/issues/{ISSUE-ID}-*/
# If Jira (PROJ): Fetch via MCP, create local directory
Read: TASK.md/BUG.md or Jira description
```

### 2. Deep Thinking Phase

```bash
# Use sequential-thinking tool
Analyze:
- Problem understanding
- Technical approach options
- Research needs (libraries, patterns, best practices)
```

**Output**: Research requirements and approach direction.

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

**Complexity scoring**: See plan-structure.md for point values and thresholds.

```bash
Write: pm/issues/{ISSUE-ID}-*/PLAN.md
```

Structure per plan-structure.md:
- Phase breakdown
- Test-first guidance
- Acceptance criteria
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
# Detection criteria: See plan-structure.md "Security Detection"
# If security-relevant: Spawn security-auditor agent
# Reviews security implications
```

### 8. Present Plan

Display:
- Phase breakdown
- Research summary (libraries used, key findings)
- Review signoffs
- Estimated complexity

## Agent Coordination

**Primary**: project-manager (analysis), test-engineer (testing strategy)
**Mandatory**: code-architect (architectural review)
**Conditional**: security-auditor (if security-relevant per plan-structure.md)

## Error Handling

- **Issue not found**: "Issue {ID} not found in pm/issues/"
- **PLAN.md exists**: Ask to overwrite or append
- **Jira fetch fails**: "Cannot fetch PROJ-{ID} - check MCP"
- **Context files missing**: Warning but continue (use defaults)

## Integration

```
/epic → /plan TASK-### → /implement TASK-### 1.1
```

**Creates**: `pm/issues/{ISSUE-ID}-*/PLAN.md`
**Next step**: `/implement {ISSUE-ID} 1.1`
