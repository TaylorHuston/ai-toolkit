---
tags: ["workflow", "planning", "implementation"]
description: "Create PLAN.md file with phase-based breakdown for individual tasks and bugs"
argument-hint: "TASK-### | BUG-### | PROJ-###"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs", "WebSearch", "WebFetch"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # Plan methodology, phase structure, TDD cycles, complexity scoring
  - docs/development/workflows/spike-workflow.md  # Spike exploration planning
  - docs/development/templates/plan-template.md  # Plan file structure
---

# /plan Command

**WHAT**: Create PLAN.md with phase-based breakdown through analysis and research.

**HOW**: Read pm-workflows.md for complete methodology (phase structure, TDD cycles, complexity scoring, strategic planning).

## Usage

```bash
/plan TASK-001              # Creates pm/issues/TASK-001-*/PLAN.md
/plan BUG-003               # Creates pm/issues/BUG-003-*/PLAN.md
/plan PROJ-123              # Jira: Fetches issue, creates PLAN.md locally
/plan --spike SPIKE-001     # Creates PLAN-N.md files for each approach
```

## Process

1. **Load**: Read TASK.md/BUG.md, parent SPEC (if exists), similar WORKLOGs
2. **Analyze**: Sequential thinking, research via Context7/web search
3. **Generate**: Phase breakdown per pm-workflows.md "Phase Structure Standards"
   - **TDD Phases** (feature work): X.RED/X.GREEN/X.REFACTOR sub-phases
   - **Simple Phases** (infra): No TDD structure
4. **Review**: code-architect (always), security-auditor (if security-relevant)
5. **Present**: Phases, scenario coverage, complexity score, research summary

## Spike Mode (--spike)

Creates multiple PLAN-N.md exploration files. See spike-workflow.md for details.

```bash
/plan --spike SPIKE-001
# Creates PLAN-1.md (approach 1), PLAN-2.md (approach 2), etc.
```

**Differences**: Multiple files, exploration phases, no TDD enforcement, no reviews.

## Example Output

```
Creating plan for TASK-001: User Login Flow
✓ Loaded parent spec SPEC-001 (3 scenarios)
✓ Research: JWT patterns, security best practices
✓ Using TDD phase structure

Phase 1 - User can log in
  1.RED: Write failing tests
  1.GREEN: Implement login endpoint
  1.REFACTOR: Clean up, review >= 90

Phase 2 - Invalid credentials error
  2.RED/2.GREEN/2.REFACTOR...

Reviews: ✓ code-architect, ✓ security-auditor
Complexity: 8 points (Medium)

Next: /implement TASK-001 1.RED
```

## Integration

```
/spec → /plan TASK-### → /implement TASK-### 1.RED
```

**Creates**: `pm/issues/{ISSUE-ID}-*/PLAN.md`
