---
tags: ["jira", "integration", "promote", "workflow"]
description: "Promote local exploration issue to Jira for team visibility"
argument-hint: "TASK-### | BUG-###"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/misc/jira-integration.md  # Jira integration and promotion workflow
  - docs/development/workflows/pm-workflows.md  # Core PM workflows and file formats
---

# /jira-promote Command

**WHAT**: Promote local exploration issue to Jira for team collaboration.

**WHY**: Convert validated prototypes to production-tracked work.

**HOW**: Migrate TASK-###/BUG-### to Jira, preserve all artifacts, update branch.

## Usage

```bash
/jira-promote TASK-001    # Promote local task to Jira
/jira-promote BUG-003     # Promote local bug to Jira
```

## Execution Flow

**Before you start**: Read jira-integration.md for Jira integration patterns, field discovery, and promotion workflow.

### High-Level Steps

1. **Validate Prerequisites**
   - Check Jira enabled in CLAUDE.md
   - Verify Atlassian MCP accessible
   - Confirm local issue exists (TASK.md or BUG.md)

2. **Load Local Artifacts**
   - Read TASK.md/BUG.md, PLAN.md, WORKLOG.md, RESEARCH.md
   - Extract summary, description, acceptance criteria

3. **Field Discovery & Collection**
   - Load field cache (.ai-toolkit/jira-field-cache.json)
   - Map local → Jira (summary, description, issue type)
   - Prompt for custom fields conversationally

4. **Create Jira Issue**
   - Use Atlassian MCP to create issue
   - Get PROJ-### issue key and URL
   - Display Jira link

5. **Migrate Artifacts**
   - Create pm/issues/PROJ-###-{name}/
   - Copy PLAN.md, WORKLOG.md, RESEARCH.md, resources/
   - Add promotion note to WORKLOG

6. **Cleanup & Branch Update**
   - Ask: Delete original directory?
   - If yes: Remove pm/issues/TASK-###-*/
   - Rename branch: feature/TASK-### → feature/PROJ-###

**See jira-integration.md "Promotion Workflow" for complete artifact migration and field mapping details.**

## When to Promote

**Promote when**: Prototype validated, ready for production, needs team collaboration
**Don't promote when**: Still exploring, throwaway spike, personal learning, already complete

## Error Handling

**Jira not enabled**: Enable in CLAUDE.md with `jira.enabled: true`
**Issue not found**: Verify local issue exists in pm/issues/
**Field discovery fails**: Field cache will auto-refresh on failure
**MCP unavailable**: Install and configure Atlassian MCP server

See jira-integration.md for complete error scenarios and solutions.
