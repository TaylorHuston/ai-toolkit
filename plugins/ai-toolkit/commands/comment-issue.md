---
tags: ["jira", "integration", "collaboration", "workflow"]
description: "Add AI-suggested comments to external Jira issues based on work context"
argument-hint: "PROJ-###"
allowed-tools: ["Read", "Write", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/jira-integration.md  # Jira integration patterns
  - docs/development/guidelines/worklog-format.md  # WORKLOG format for context gathering
---

# /comment-issue Command

## WHAT
Add AI-suggested comments to external Jira issues by analyzing local work context (WORKLOG, commits, existing comments).

## WHY
Keeps stakeholders informed with professional progress updates synthesized from actual work, without manual comment composition.

**Scope:** External issues only (PROJ-###). Use `/comment` for local issues (TASK-###, BUG-###).

## HOW

### Usage
```bash
/comment-issue PROJ-123              # AI suggests comment based on context
/comment-issue PROJ-123 "text"       # Add specific comment text
```

### Pre-Execution Context

**Validate prerequisites:**
- CLAUDE.md: `jira.enabled: true`
- Atlassian Remote MCP: Available
- Issue ID: External format (PROJ-###, not TASK-###)
- Jira access: Issue exists and accessible

**Gather context (interactive mode only):**
- WORKLOG: `pm/issues/PROJ-123-*/WORKLOG.md` (last 5-10 entries)
- Git commits: Last 10 mentioning issue ID
- Jira comments: Existing comments (avoid duplication)

### Execution Steps

**1. Validate:**
```bash
# Read CLAUDE.md jira config
# Check issue ID format (must be PROJ-###)
# Verify Atlassian MCP available
# Confirm issue exists in Jira
```

**2. Interactive mode (no text provided):**
```bash
# Find issue directory
glob pm/issues/PROJ-###-*/

# Read WORKLOG
# Get recent commits: git log -10 --grep="PROJ-###"
# Fetch Jira comments (optional, for context)

# AI synthesizes comment:
# Format:
# [Summary sentence]
#
# Work completed:
# - [Accomplishment 1]
# - [Accomplishment 2]
#
# Files changed:
# - path/to/file (new/modified)
#
# Next steps:
# - [Action 1]
# - [Action 2]

# Show preview
# Ask: "Post this comment? (yes/edit/cancel)"
```

**3. Direct mode (text provided):**
```bash
# Show preview of provided text
# Ask: "Post to Jira? (yes/cancel)"
```

**4. Handle user approval:**
- `yes`: Post comment via Atlassian MCP
- `edit`: Allow modification, then confirm
- `cancel`: Exit without posting

**5. Post to Jira:**
```bash
# Use Atlassian MCP to add comment
# Get confirmation and URL
# Optionally log to WORKLOG: "Posted update to Jira"
# Display: "✓ Comment posted to PROJ-###"
```

### Comment Format Guidelines

**Template:**
```
[Summary sentence]

Work completed:
- [Accomplishment 1]
- [Accomplishment 2]

Files changed:
- path/to/file (new/modified)

Next steps:
- [Action 1]
```

**Characteristics:**
- Concise: 5-10 lines total
- Actionable: Focus on stakeholder needs
- Specific: Include file paths, metrics
- Forward-looking: Always suggest next steps
- Professional: Assumes PM/stakeholder audience

### Error Handling

**Local issue provided:**
```
Error: TASK-001 is a local issue.
Use /comment for local issues.
/comment-issue only works with PROJ-###.
```

**Jira not enabled:**
```
Error: Jira integration not enabled.
Add to CLAUDE.md:
  jira.enabled: true
  jira.project_key: PROJ
Configure Atlassian Remote MCP.
```

**MCP unavailable:**
```
Error: Atlassian Remote MCP not configured.
Setup guide: https://www.atlassian.com/blog/announcements/remote-mcp-server
```

**No context found:**
```
Warning: No local work context for PROJ-456.
- No WORKLOG entries
- No git commits mentioning issue

Use direct mode: /comment-issue PROJ-456 "text"
Or start working: /plan PROJ-456
```

**Permission denied:**
```
Error: Failed to add comment (403 Forbidden).
Possible: No permission, issue locked, credentials expired.
Check: {jira_url}
```

**Issue not found:**
```
Error: PROJ-456 not found in Jira.
Possible: Doesn't exist, no permission, wrong project key.
```

### Integration

**Workflow position:**
```
/implement PROJ-456 1.2 → /comment-issue PROJ-456 → /implement PROJ-456 1.3
```

**When to use:**
- After significant work
- End of day updates
- When blocked
- Stakeholder requests
- Not for every commit (too frequent)

### Related
- `/comment` - Local WORKLOG entry (not Jira)
- `/import-issue` - Import Jira issue to local
- `/plan PROJ-###` - Create implementation plan
- `/implement PROJ-### 1.1` - Execute work
