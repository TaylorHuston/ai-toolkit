---
tags: ["jira", "integration", "promote", "workflow"]
description: "Promote local exploration issue to Jira for team visibility"
argument-hint: "TASK-### | BUG-###"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/pm-guidelines.md  # Jira integration and promotion workflow
  - docs/development/guidelines/issue-management.md  # Local issue structure and migration
---

# /promote Command

**WHAT**: Promote local exploration issue to Jira for team collaboration.

**WHY**: Convert validated prototypes to production-tracked work.

**HOW**: Migrate TASK-###/BUG-### to Jira, preserve all artifacts, update branch.

## Usage

```bash
/promote TASK-001    # Promote local task to Jira
/promote BUG-003     # Promote local bug to Jira
```

## Requirements

- Jira enabled in CLAUDE.md
- Atlassian Remote MCP configured
- Local issue exists (TASK.md or BUG.md)
- Field cache available (auto-created if missing)

## Execution Steps

### 1. Validate Prerequisites

```bash
# Check Jira enabled
Read: CLAUDE.md  # jira.enabled: true

# Verify MCP available
# Check Atlassian MCP tools accessible

# Verify local issue exists
Glob: pm/issues/{ISSUE-ID}-*/
Read: TASK.md or BUG.md
```

### 2. Load Local Issue

```bash
Read: TASK.md or BUG.md  # Description, acceptance criteria
Read: PLAN.md            # If exists
Read: WORKLOG.md         # If exists
Read: RESEARCH.md        # If exists
```

### 3. Discover Jira Fields

```bash
# Check cache
Read: .ai-toolkit/jira-field-cache.json

# If missing: Fetch via MCP (same as /epic)
# Determine issue type: TASK → Story/Task, BUG → Bug
```

### 4. Collect Fields Conversationally

**Map local to Jira:**
- Summary: From TASK.md title
- Description: From TASK.md description + acceptance criteria
- Issue Type: Story/Task/Bug (ask if TASK)
- Custom fields: Prompt based on cache

### 5. Create in Jira

```bash
# Use Atlassian MCP
Create issue with collected fields
Get PROJ-### issue key
Display Jira URL
```

### 6. Migrate Artifacts

```bash
# Create new directory
mkdir pm/issues/PROJ-###-{name}/

# Copy files
cp PLAN.md pm/issues/PROJ-###-{name}/
cp WORKLOG.md pm/issues/PROJ-###-{name}/
cp RESEARCH.md pm/issues/PROJ-###-{name}/  # If exists
cp -r resources/ pm/issues/PROJ-###-{name}/  # If exists

# Add promotion note to WORKLOG
Edit: WORKLOG.md
```

**Promotion note format:**
```markdown
## 2025-10-31 14:30 - system

Promoted from TASK-001 to PROJ-456.
Jira: https://company.atlassian.net/browse/PROJ-456
```

### 7. Cleanup & Branch Update

**Ask user**: Delete original directory? (yes/no)

If yes: `rm -rf pm/issues/TASK-###-*/`

**Update branch** (if on feature/TASK-###):
```bash
git branch --show-current
git branch -m feature/TASK-001 feature/PROJ-456
```

## Example Output

```
Promoting TASK-001 to Jira...

✓ Read TASK-001: OAuth Implementation Spike
✓ Loaded Jira field cache

Your Jira requires:
- Summary: "OAuth Implementation Spike" ✓
- Issue Type: Story / Task? → Story
- Team: Frontend / Backend / DevOps → Backend

✓ Created PROJ-456: OAuth Implementation Spike
🔗 https://company.atlassian.net/browse/PROJ-456

✓ Created pm/issues/PROJ-456-oauth-implementation/
✓ Copied PLAN.md, WORKLOG.md
✓ Added promotion note

Delete original TASK-001? (yes/no) → yes
✓ Deleted pm/issues/TASK-001-oauth-spike/
✓ Renamed branch: feature/TASK-001 → feature/PROJ-456

Next: /implement PROJ-456 2.1
```

## When to Promote

**Promote when:**
- Prototype validated and working
- Ready for production
- Needs team collaboration
- Stakeholders want visibility

**Don't promote when:**
- Still exploring
- Throwaway spike
- Personal learning
- Already complete (keep local)

## Integration

```
Local Exploration → Promotion → Team Collaboration

/plan TASK-001 → /implement → Validate → /promote → PROJ-###
```

## Error Handling

**Jira not enabled:**
```
Error: Jira not enabled in CLAUDE.md
Enable with: jira.enabled: true
```

**Issue not found:**
```
Error: TASK-001 not found
Available: TASK-002, BUG-001
```

**Field discovery fails:**
```
Error: Field 'customfield_10099' required
Fix: /refresh-schema and retry
```

**MCP unavailable:**
```
Error: Atlassian MCP not configured
Setup: Install MCP server and configure
```

## Related Commands

- `/refresh-schema` - Update Jira field cache
- `/import-issue PROJ-###` - Opposite (Jira → local)
- `/epic` - Create epic directly in Jira

## Notes

- Preserves all local artifacts
- User confirms cleanup and branch rename
- Uses same field discovery as `/epic`
- Safe to retry if creation fails
