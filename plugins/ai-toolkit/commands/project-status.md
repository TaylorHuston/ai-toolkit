---
tags: ["workflow", "project-status", "analysis", "context", "dashboard"]
description: "Enhanced project status dashboard with intelligent context analysis"
argument-hint: "[--ai-format] [--detailed]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - pm/README.md
---

# /project-status Command

## WHAT
Enhanced project status dashboard with intelligent analysis - git state, project health, workflow progress, environment consistency, and Jira integration.

## WHY
Provides comprehensive project context through intelligent agent analysis, enabling informed decisions and workflow continuity.

## HOW

### Usage
```bash
/project-status                # Standard human-readable report
/project-status --ai-format    # AI-optimized format
/project-status --detailed     # Comprehensive analysis
```

### Pre-Execution Context

**Gather project state:**
- Git: Branch, status, recent commits, remotes
- PM structure: Parse `pm/epics/`, `pm/issues/` for progress
- CLAUDE.md: Jira integration settings, project context
- Environment: Tools, dependencies, versions
- Recent changes: Modified files, active work

**Check Jira integration:**
- Read CLAUDE.md `jira.enabled` flag
- If enabled: Query Jira via Atlassian MCP
- Match Jira issues with local directories

### Execution Steps

**1. Collect status data:**
```bash
# Git
git branch --show-current
git status --porcelain
git log -10 --oneline
git remote -v

# PM structure
glob: pm/specs/*/
glob: pm/issues/*/
# Parse PLAN.md phases, WORKLOG.md entries

# Environment
# Detect: package.json, requirements.txt, Cargo.toml, etc.
# Check versions, outdated deps

# Jira (if enabled)
# Use Atlassian MCP to query project issues
# Match with local directories
```

**3. Jira hybrid display (if enabled):**
```
## Epics (from Jira)
- PROJ-100: Feature Name (Jira: In Progress)
  - 4/6 issues complete locally (66%)

## Issues

Jira Issues:
- PROJ-123: Task Name [IN PROGRESS] (Jira: In Progress)
- PROJ-124: Bug Fix [COMPLETED] (Jira: In Progress)
  ⚠️ Local complete, update Jira status

Local Exploration:
- TASK-001: Spike [COMPLETED]
- TASK-002: Experiment [IN PROGRESS]

Legend:
- [PLANNED] = PLAN.md exists
- [IN PROGRESS] = WORKLOG.md exists
- [COMPLETED] = All PLAN.md phases checked
- (Jira: X) = Current Jira status (read-only)
```

**3. Intelligent analysis:**
- Branch strategy assessment
- Commit pattern insights
- Technical debt identification
- Workflow phase analysis
- Blocker identification
- Risk assessment
- Actionable recommendations

**4. Format output:**
- Standard: Human-readable with visual indicators
- AI-format: Structured for AI consumption
- Detailed: Comprehensive with trends and rationale
- JSON: Programmatic access

### Status Dimensions

**Git Intelligence:**
- Branch, commits, status (basic)
- Branch strategy, commit patterns, merge readiness (enhanced)

**Project Health:**
- File counts, structure (basic)
- Code quality trends, technical debt, architecture health (enhanced)

**Workflow State:**
- Current files, recent changes (basic)
- Workflow phase, task progress, blockers (enhanced)

**Environment Analysis:**
- Tools, dependencies (basic)
- Consistency, vulnerabilities, optimization (enhanced)

### Agent Coordination

**Primary:** project-manager (orchestration and analysis)
**Supporting:** technical-writer (formatting)

### Error Handling

**Jira MCP unavailable:**
```
Warning: Jira enabled but Atlassian MCP unavailable.
Showing local issues only.
```

**Jira query failure:**
```
Warning: Could not fetch Jira issues.
Reason: {error}
Showing local issues only.
```

**No PM structure:**
```
Info: No pm/ directory found.
Project not using PM workflow.
Showing git and environment status only.
```

### Integration

**Workflow position:** Context refresh for any command
**Use cases:**
- Session start: Get project overview
- Before planning: Assess readiness
- During implementation: Check progress
- Before commit: Validate state
- AI context refresh: Preserve continuity