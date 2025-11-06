---
tags: ["jira", "integration", "import", "workflow"]
description: "Import Jira issue for local work with PLAN.md creation"
argument-hint: "PROJ-###"
allowed-tools: ["Read", "Write", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/pm-guidelines.md  # Jira integration and import workflow
  - docs/development/guidelines/issue-management.md  # Issue directory structure and naming
---

# /import-issue Command

## WHAT
Import Jira issue into local structure, creating directory for implementation artifacts and displaying requirements.

## WHY
Enables AI-assisted implementation of PM/stakeholder-created issues with local artifact management.

**Note:** Not required - `/plan PROJ-123` auto-imports. Use this to preview before planning or verify access.

## HOW

### Usage
```bash
/import-issue PROJ-123    # Import specific Jira issue (or epic with bulk import)
```

### Pre-Execution Context

**Validate prerequisites:**
- CLAUDE.md: `jira.enabled: true`
- Atlassian Remote MCP: Available
- Issue ID: Valid format (PROJ-###)
- Jira access: Issue exists and accessible

### Execution Steps

**1. Fetch from Jira:**
```bash
# Use Atlassian MCP to get issue
# Fields: key, summary, description, type, status, epic_link, reporter, assignee
# If type = Epic: Fetch all child issues in epic
```

**2. Create local directory:**
```bash
# Convert summary to kebab-case
# If regular issue (Story/Task/Bug):
#   mkdir pm/issues/PROJ-123-{kebab-case-summary}/
# If Epic:
#   mkdir pm/epics/PROJ-123-{kebab-case-name}/
# No TASK.md created (Jira is source of truth)
```

**3. Display issue details:**
```
Issue: PROJ-123
Summary: {summary}
Type: {type}
Status: {status}
Epic: {epic_link} (if present)

Description:
{description}

Acceptance Criteria:
{parsed from description or custom field}

Local: pm/issues/PROJ-123-{name}/

Next: /plan PROJ-123
View: {jira_url}
```

**4. Bulk import (Epics only):**
```bash
# If issue type is Epic:
# Show child issues (key, summary, type, status)
# Ask: "Import all N child issues? (yes/no)"
# If yes:
#   For each child:
#     - Check if already imported (directory exists)
#     - Fetch and create directory if new
#     - Display progress
#   Show summary (new, skipped, total)
```

**5. Ready for work:**
- `/plan PROJ-123` - Create implementation plan
- `/implement PROJ-123 1.1` - Start work

### Directory Structure

**Regular issues:**
```
pm/issues/PROJ-456-api-rate-limiting/
└── (empty - ready for PLAN.md, WORKLOG.md, RESEARCH.md)
```

**Epics:**
```
pm/epics/PROJ-400-api-infrastructure/
└── (empty - epic metadata, Jira is source)
```

**Naming:** kebab-case, strip special chars, max 50 chars

### Error Handling

**Issue not found:**
```
Error: PROJ-456 not found in Jira.
Possible: Doesn't exist, no permission, wrong project key.
Check: {jira_url}
```

**Jira not enabled:**
```
Error: Jira integration not enabled.
Add to CLAUDE.md:
  jira.enabled: true
  jira.project_key: PROJ
Configure Atlassian Remote MCP.
Or work locally: /plan TASK-001
```

**MCP unavailable:**
```
Error: Atlassian Remote MCP not configured.
Setup guide: https://www.atlassian.com/blog/announcements/remote-mcp-server
```

**Directory exists:**
```
Warning: pm/issues/PROJ-456-{name}/ already exists.
Previously imported. Contents: PLAN.md, WORKLOG.md
Issue details (refreshed from Jira): {details}
Continue: /plan PROJ-456 or /implement PROJ-456 1.1
```

### Integration

**Workflow position:**
```
Jira (PM creates) → /import-issue PROJ-456 → /plan PROJ-456 → /implement
```

**Alternative (auto-import):**
```
Jira (PM creates) → /plan PROJ-456 (auto-imports) → /implement
```

**Use /import-issue when:**
- Preview before planning
- Verify access/existence
- See acceptance criteria upfront
- Import epic with bulk child import

**Skip when:**
- Ready to plan: `/plan PROJ-456` auto-imports
- Trust issue exists

### Related
- `/plan PROJ-123` - Create plan (auto-imports)
- `/implement PROJ-123 1.1` - Execute phase
- `/project-status` - View all imported issues
- `/promote TASK-001` - Convert local to Jira
