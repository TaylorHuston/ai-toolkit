---
tags: ["jira", "integration", "cache", "maintenance"]
description: "Refresh Jira field schema cache when requirements change"
argument-hint: ""
allowed-tools: ["Read", "Write", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/pm-guidelines.md  # Jira field caching and schema management
---

# /refresh-schema Command

**WHAT**: Refresh cached Jira field schema from current Jira configuration.

**WHY**: Jira admins add/modify required fields, causing creation failures.

**HOW**: Delete cache, fetch fresh schema via MCP, save to `.ai-toolkit/jira-field-cache.json`.

## Usage

```bash
/refresh-schema    # Refresh field cache from Jira
```

## Requirements

- Jira enabled in CLAUDE.md
- Atlassian Remote MCP configured
- Network connectivity to Jira

## Execution Steps

### 1. Validate Prerequisites

```bash
Read: CLAUDE.md  # jira.enabled: true
# Check Atlassian MCP available
# Test Jira connectivity (simple API call)
```

### 2. Clear Cache

```bash
Bash: rm .ai-toolkit/jira-field-cache.json
# If not exists: Log "No cache found (OK)" and continue
```

### 3. Fetch Schema

```bash
# Use Atlassian MCP to query each issue type
For Epic, Story, Task, Bug:
  - Get field requirements
  - Parse required vs optional
  - Extract allowed values
```

**Cache structure:**
```json
{
  "last_updated": "2025-10-31T14:30:00Z",
  "project_key": "PROJ",
  "issue_types": {
    "epic": {
      "fields": {
        "summary": {"required": true, "type": "string"},
        "customfield_10099": {
          "required": true,
          "type": "option",
          "name": "Team",
          "allowedValues": ["Frontend", "Backend"]
        }
      }
    }
  }
}
```

### 4. Write Cache

```bash
# Create directory if needed
mkdir -p .ai-toolkit/

# Write formatted JSON
Write: .ai-toolkit/jira-field-cache.json
# Set timestamp to now
```

### 5. Display Summary

```
✓ Epic fields (8 required)
✓ Story fields (6 required)
✓ Task fields (5 required)
✓ Bug fields (6 required)

New required fields:
- Team Assignment (customfield_10099)

✓ Cached to .ai-toolkit/jira-field-cache.json
Next: Retry your command (e.g., /epic)
```

## When to Use

**Use when:**
- `/epic` or `/promote` fails with missing field error
- Jira admin announces schema changes
- Starting with new Jira project
- Monthly maintenance

**Don't use when:**
- Jira working fine
- Before every command (auto-cached)
- Jira integration disabled

## Cache Lifecycle

**Creation**: Auto-created on first `/epic` or `/plan PROJ-###`
**Expiration**: Never (manual refresh only)
**Location**: `.ai-toolkit/jira-field-cache.json`
**Version control**: Add to `.gitignore`

## Error Handling

**Jira not enabled:**
```
Error: Jira not enabled in CLAUDE.md
```

**MCP unavailable:**
```
Error: Atlassian MCP not configured
Setup: Install MCP server and configure
```

**Network error:**
```
Error: Cannot connect to Jira
Check: VPN, credentials, permissions
```

**Fetch fails:**
```
Error: Failed to fetch schema
Reason: Project 'PROJ' not found
Check: CLAUDE.md project key
```

## Related Commands

- `/epic` - Uses field cache
- `/promote TASK-###` - Uses field cache
- `/plan PROJ-###` - Uses field cache

## Notes

- Safe operation (doesn't modify Jira)
- Auto-suggested when creation fails
- First Jira command auto-creates cache
- Manual refresh forces update
