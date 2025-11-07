---
target_audience: ["development-team"]
document_type: "integration-guide"
priority: "medium"
tags: ["jira", "integration", "atlassian", "project-management"]
---

# Jira Integration Guide

The AI Toolkit integrates with Atlassian Jira for teams using Jira as their project management system.

**Current Status**: Minimal integration. Tested with one Jira project. Does not include workflow transitions or automated status updates.

## Requirements

- **Atlassian Remote MCP Server** configured in Claude Code
- Jira Cloud account with appropriate permissions
- Project Key (e.g., "PROJ") for your Jira project

## Setup

### 1. Configure Atlassian Remote MCP Server

Follow [Atlassian's guide](https://www.atlassian.com/blog/announcements/remote-mcp-server) to configure the MCP server in Claude Code.

### 2. Enable Jira in CLAUDE.md

Add to your project's CLAUDE.md:

```yaml
## Jira Integration
- **Enabled**: true
- **MCP Server**: Atlassian Remote MCP
- **Project Key**: PROJ
```

### 3. Run Your First Command

```bash
/epic  # Creates epic in Jira
```

## How It Works

### Epics → Jira Only

- All epics live in Jira (PROJ-100, PROJ-200)
- Use `/epic` to create epics conversationally
- AI discovers required fields automatically

### Issues → Jira or Local

- **Jira issues** (PROJ-123): Import with `/import-issue PROJ-123`
- **Local exploration** (TASK-001): Create with `/plan TASK-001`
- **Promotion**: Use `/promote TASK-001` to create Jira issue from local spike

### What Syncs

- ✅ **Read from Jira**: Description, acceptance criteria, status (display only)
- ✅ **Create in Jira**: Epics, stories, tasks via AI conversation
- ✅ **Add comments**: AI-suggested comments based on local work context
- ❌ **Not synced**: Status updates, field changes, automatic comment sync

### Local Storage

- `PLAN.md` - AI implementation phases (local only)
- `WORKLOG.md` - Implementation history (local only)
- `RESEARCH.md` - Technical decisions (local only)
- No TASK.md for Jira issues (fetched on-demand)

## Common Workflows

### Working on Jira Issue

```bash
/import-issue PROJ-123         # Pull from Jira
/plan PROJ-123                 # Create implementation plan
/implement PROJ-123 1.1        # Execute phase
/commit                        # Commit changes
/comment-issue PROJ-123        # AI suggests progress comment for Jira
# Update Jira status manually in UI
```

### Exploration Then Promotion

```bash
/plan TASK-001                 # Quick local spike
/implement TASK-001 1.1        # Prototype
/promote TASK-001              # Create PROJ-125 in Jira
/plan PROJ-125                 # Continue with Jira issue
```

### Creating Epics + Issues

```bash
/epic                          # AI guides epic creation
→ Creates PROJ-100 in Jira
→ Optionally creates initial issues
/plan PROJ-123                 # Start implementation
```

## Commands

### `/import-issue PROJ-###`

Import Jira issue for local work with automatic PLAN.md creation.

**What it does**:
- Fetches issue details from Jira
- Creates `pm/issues/PROJ-###-*/` directory
- Creates PLAN.md for implementation planning
- Displays issue description and acceptance criteria

**When to use**:
- Starting work on assigned Jira issue
- Need to create implementation plan for Jira work

### `/promote TASK-### | BUG-###`

Promote local exploration issue to Jira for team visibility.

**What it does**:
- Creates new Jira issue from local TASK/BUG
- Copies description and acceptance criteria
- Returns new Jira issue key (e.g., PROJ-125)
- Suggests next steps with Jira issue

**When to use**:
- Local spike proved valuable
- Work should be tracked in Jira
- Need team visibility on exploration

### `/comment-issue PROJ-###`

Add AI-suggested comments to Jira issues based on work context.

**What it does**:
- Analyzes WORKLOG.md and recent commits
- Generates progress summary
- Suggests comment text
- User confirms before posting

**When to use**:
- After completing implementation work
- Providing status updates to team
- Documenting technical decisions in Jira

### `/refresh-schema`

Refresh Jira field schema cache when requirements change.

**What it does**:
- Clears cached Jira field definitions
- Fetches latest required/optional fields
- Updates field validation rules

**When to use**:
- Getting "Field 'X' is required" errors
- Jira admin added new required fields
- Field validation seems incorrect

## Limitations

### Status Updates

**Not automated**. Update Jira status manually in UI.

**Why**: Workflow transitions are project-specific and may have complex rules/required fields.

**Workaround**: Update status in Jira UI after completing work.

### Comment Sync

**Manual only**. Use `/comment-issue` to manually add comments.

**Why**: Local WORKLOG.md contains implementation details not always relevant to Jira audience.

**Workaround**: Use `/comment-issue` which suggests appropriate public comments.

### Custom Workflows

**AI doesn't trigger workflow transitions**.

**Why**: Each Jira project has unique workflows with specific transition rules.

**Workaround**: Handle transitions manually in Jira UI.

### Offline Work

**Can work on existing issues with cached data, but can't create new Jira issues or add comments offline**.

**Why**: Requires network connection to Atlassian Remote MCP Server.

**Workaround**: Use local TASK-### issues for offline work, promote later with `/promote`.

### One Project

**Currently supports one Jira project key per repository**.

**Why**: Single project key configured in CLAUDE.md.

**Workaround**: For multi-project work, configure different project key per repository or manually specify in commands.

## Troubleshooting

### "Atlassian MCP unavailable"

**Causes**:
- Atlassian Remote MCP Server not configured in Claude Code MCP settings
- Network/VPN connection issues
- Jira authentication expired

**Solutions**:
1. Check MCP server configuration in Claude Code settings
2. Verify network connectivity
3. Re-authenticate with Atlassian if needed
4. Check VPN connection if required

### "Field 'X' is required"

**Causes**:
- Jira admin added new required fields
- Field schema cache is stale
- Different issue type has different requirements

**Solutions**:
1. Run `/refresh-schema` to update field cache
2. Check Jira issue type requirements
3. Verify field values in Jira UI

### "Issue not found"

**Causes**:
- Issue doesn't exist in Jira
- Wrong project key in CLAUDE.md
- Insufficient permissions to view issue

**Solutions**:
1. Verify issue exists in Jira
2. Check project key is correct in CLAUDE.md
3. Ensure you have permission to view issue
4. Check issue isn't in a restricted project

### Commands fail silently

**Causes**:
- Jira integration not enabled in CLAUDE.md
- MCP server not responding
- Network timeouts

**Solutions**:
1. Verify `Enabled: true` in CLAUDE.md Jira section
2. Test MCP connection manually
3. Check Claude Code logs for errors
4. Restart Claude Code if MCP seems stuck

## Future Enhancements

Planned improvements for Jira integration:

- **Workflow transitions**: Automated status updates
- **Bidirectional sync**: Automatic comment and field sync
- **Multi-project support**: Multiple Jira projects per repository
- **Offline queue**: Queue Jira operations for later sync
- **Custom field mapping**: Configure field mappings in CLAUDE.md
- **Attachment sync**: Sync screenshots and files to Jira

## Feedback

This integration is experimental and evolving. Please report issues and suggestions via:
- GitHub Issues for bugs
- GitHub Discussions for feature requests
