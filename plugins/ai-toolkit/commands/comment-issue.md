---
tags: ["jira", "integration", "collaboration", "workflow"]
description: "Add AI-suggested comments to external Jira issues based on work context"
argument-hint: "PROJ-###"
allowed-tools: ["Read", "Write", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
---

# /comment-issue Command

Add AI-suggested comments to external Jira issues based on recent work context.

## Purpose

Post progress updates to Jira issues by analyzing local work (WORKLOG entries, git commits, existing Jira comments) and generating contextual comment suggestions.

**Use this when:**
- You want to update stakeholders on progress in Jira
- You've completed work and want to document it externally
- You need to add context or blockers to a Jira issue
- You want AI to synthesize recent work into a concise update

**This does NOT:**
- Work with local issues (TASK-###, BUG-###) - use `/comment` for those
- Update issue status or fields - only adds comments
- Auto-post comments - always requires user approval

## Usage

```bash
/comment-issue PROJ-123              # AI suggests comment based on context
/comment-issue PROJ-123 "text"       # Add specific comment text
```

## Requirements

- **Jira integration enabled** in CLAUDE.md
- **Atlassian Remote MCP** configured
- **External issue only** (PROJ-###, not TASK-### or BUG-###)
- **Issue must exist** in Jira and be accessible

## What It Does

### Interactive Mode (No Text Provided)

1. **Validates prerequisites**:
   - Jira enabled in CLAUDE.md
   - Atlassian MCP available
   - Issue ID is external (PROJ-###)

2. **Gathers context**:
   - Reads recent WORKLOG entries (`pm/issues/PROJ-123-*/WORKLOG.md`)
   - Gets recent git commits (last 10 on current branch)
   - Fetches existing Jira comments to avoid duplication

3. **AI analyzes and suggests**:
   - Synthesizes work context into concise update
   - Formats with sections: Summary, Work Done, Files Changed, Next Steps
   - Shows suggested comment to user

4. **User approval**:
   - User can: approve, edit, or cancel
   - If edit: modify text before posting
   - If cancel: exit without posting

5. **Posts to Jira**:
   - Uses Atlassian MCP to add comment
   - Confirms success with Jira URL
   - Optionally records in WORKLOG that comment was posted

### Direct Mode (Text Provided)

1. **Validates prerequisites** (same as interactive)
2. **Shows preview** of comment to be posted
3. **User approval** (yes/cancel)
4. **Posts to Jira** immediately

## Workflow Examples

### Example 1: Interactive Comment Suggestion

```bash
# After working on PROJ-456 for a few hours
/comment-issue PROJ-456

AI: Analyzing recent work on PROJ-456...

    Context gathered:
    - WORKLOG entries: 3 (last 2 days)
    - Git commits: 5 (mentioning PROJ-456)
    - Jira comments: 2 existing

    Suggested comment:
    ┌─────────────────────────────────────────────────────────
    │ Rate limiting implementation in progress.
    │
    │ Work completed:
    │ - Redis middleware for distributed request counting
    │ - HTTP 429 responses with Retry-After headers
    │ - Rate limit config: 100/min per user, 1000/min per IP
    │
    │ Files changed:
    │ - src/middleware/rate-limit.ts (new)
    │ - src/config/limits.ts (new)
    │ - tests/rate-limit.test.ts (new)
    │
    │ Next steps:
    │ - Complete integration tests
    │ - Add monitoring for rate limit hits
    │ - Update API documentation
    └─────────────────────────────────────────────────────────

    Post this comment to PROJ-456? (yes/edit/cancel)

User: yes

AI: ✓ Comment posted to PROJ-456
    View: https://company.atlassian.net/browse/PROJ-456
```

### Example 2: Direct Comment with Text

```bash
/comment-issue PROJ-456 "Blocked on Redis setup - need dev environment access"

AI: Preview comment for PROJ-456:
    ┌─────────────────────────────────────────────────────────
    │ Blocked on Redis setup - need dev environment access
    └─────────────────────────────────────────────────────────

    Post to Jira? (yes/cancel)

User: yes

AI: ✓ Comment posted to PROJ-456
    View: https://company.atlassian.net/browse/PROJ-456
```

### Example 3: Edit Before Posting

```bash
/comment-issue PROJ-456

AI: [Shows suggested comment]

    Post this comment to PROJ-456? (yes/edit/cancel)

User: edit

AI: Enter your comment (or modify the suggestion above):

User: Rate limiting complete and deployed to staging.

Testing in progress with load simulation (1500 req/min).

Will monitor for 24h before production deploy.

AI: ✓ Comment posted to PROJ-456
    View: https://company.atlassian.net/browse/PROJ-456
```

## Command Behavior

### Prerequisites Check

1. **Validate Jira enabled**:
   - Read CLAUDE.md
   - Check `jira.enabled: true`
   - If disabled: Error "Jira integration not enabled"

2. **Validate MCP**:
   - Check for Atlassian MCP tools
   - If unavailable: Error with setup instructions

3. **Validate issue type**:
   - Must be external issue (matches project key prefix)
   - If TASK-### or BUG-###: Error "Use /comment for local issues"
   - If invalid format: Error "Invalid issue ID format"

### Context Gathering (Interactive Mode Only)

1. **Find issue directory**:
   ```bash
   glob: pm/issues/PROJ-456-*/
   ```

2. **Read WORKLOG**:
   - Read `WORKLOG.md` if exists
   - Parse last 5-10 entries for recent context
   - Extract: what was done, files changed, gotchas, lessons

3. **Get git commits**:
   ```bash
   git log -10 --no-merges --format="%h - %s"
   # Filter for commits mentioning issue ID
   ```

4. **Fetch Jira comments** (optional):
   - Use Atlassian MCP to get existing comments
   - Avoid repeating information already in Jira
   - Maintain conversation continuity

### AI Comment Generation

**Format template:**
```
[Summary sentence of progress/status]

Work completed:
- [Key accomplishment 1]
- [Key accomplishment 2]
- [Key accomplishment 3]

Files changed:
- path/to/file1.ts (new/modified)
- path/to/file2.ts (modified)

Next steps:
- [Next action 1]
- [Next action 2]
```

**Guidelines:**
- **Concise**: 5-10 lines total, not a novel
- **Actionable**: Focus on what matters to stakeholders
- **Specific**: Include file paths, metrics, concrete details
- **Forward-looking**: Always suggest next steps
- **Professional**: Assume PM/stakeholders will read

### User Approval

**Options:**
- `yes` - Post comment as-is
- `edit` - Modify comment text before posting
- `cancel` - Exit without posting

**Edit flow:**
1. Display suggested comment
2. Allow user to modify text
3. Show preview of edited comment
4. Confirm before posting

### Post to Jira

**Use Atlassian MCP:**
- Add comment to issue key (PROJ-456)
- Handle errors gracefully
- Return comment ID and URL

**Error handling:**
- Permission denied: Clear error with Jira URL
- Network failure: Suggest retry
- Issue not found: Verify issue exists in Jira

## Error Handling

### Local Issue Provided

```
Error: TASK-001 is a local issue.

Use /comment for local issues instead:
  /comment "Your work log entry here"

This adds to: pm/issues/TASK-001-*/WORKLOG.md

/comment-issue only works with external Jira issues (PROJ-###).
```

### Jira Not Enabled

```
Error: Jira integration not enabled.

To enable:
1. Add to CLAUDE.md:
   ## Jira Integration
   - **Enabled**: true
   - **Project Key**: PROJ
2. Configure Atlassian Remote MCP

Or use local workflow: /comment "work log entry"
```

### MCP Unavailable

```
Error: Atlassian Remote MCP not configured.

Setup:
1. Install Atlassian Remote MCP Server
2. Configure in Claude Code MCP settings
3. Restart Claude Code

Guide: https://www.atlassian.com/blog/announcements/remote-mcp-server
```

### No Context Found

```
Warning: No local work context found for PROJ-456.

- No WORKLOG entries
- No git commits mentioning PROJ-456

You can still add a comment manually:
  /comment-issue PROJ-456 "your comment text"

Or start working first:
  /plan PROJ-456
  /implement PROJ-456 1.1
```

### Permission Denied

```
Error: Failed to add comment to PROJ-456.
Reason: 403 Forbidden

Possible causes:
- No permission to comment on issue
- Issue is locked or closed
- Jira credentials expired

Check issue status: https://company.atlassian.net/browse/PROJ-456
Contact admin if you should have access.
```

### Issue Not Found

```
Error: PROJ-456 not found in Jira.

Possible causes:
- Issue doesn't exist
- No permission to view
- Wrong project key in CLAUDE.md

Check: https://company.atlassian.net/browse/PROJ-456
```

## Integration with Workflow

**Position:** After completing work, before moving to next task

```
/implement PROJ-456 1.2 → (complete phase) → /comment-issue PROJ-456 → /implement PROJ-456 1.3
```

**Use cases:**
- **End of day update**: Summarize progress for stakeholders
- **Blocker notification**: Report impediments needing attention
- **Milestone completion**: Document major achievements
- **Handoff communication**: Pass context to other team members
- **Status sync**: Keep Jira in sync with actual work

**When to use:**
- ✅ After completing significant work
- ✅ Before switching tasks or end of day
- ✅ When blocked and need help
- ✅ When stakeholders ask for updates
- ❌ For every git commit (too frequent)
- ❌ For local-only work (use `/comment`)

## Implementation Notes

When implementing `/comment-issue PROJ-###`:

1. **Parse arguments**:
   - $1 = Issue ID (required, PROJ-###)
   - $2+ = Comment text (optional, for direct mode)

2. **Validate prerequisites**:
   - Read CLAUDE.md: Check `jira.enabled: true`, get project key
   - Check issue ID: Must match project key prefix (not TASK/BUG)
   - Check MCP: Ensure Atlassian MCP tools available

3. **Determine mode**:
   - If $2 provided: Direct mode (use provided text)
   - If $2 empty: Interactive mode (AI suggests comment)

4. **Interactive mode - gather context**:
   - Find issue directory: `glob pm/issues/PROJ-###-*/`
   - Read WORKLOG.md (last 5-10 entries)
   - Get git commits: `git log -10 --grep="PROJ-###"`
   - Fetch Jira comments (optional, for context)

5. **Interactive mode - generate suggestion**:
   - Analyze WORKLOG entries for work done
   - Extract file changes from git commits
   - Synthesize into structured comment
   - Format: Summary, Work Done, Files Changed, Next Steps
   - Keep concise (5-10 lines)

6. **Display and get approval**:
   - Show comment preview in box
   - Ask: "Post this comment to PROJ-###? (yes/edit/cancel)"
   - Handle user choice:
     - yes: Proceed to post
     - edit: Allow modification, then confirm
     - cancel: Exit without posting

7. **Post to Jira**:
   - Use Atlassian MCP to add comment
   - Handle errors (permissions, network, not found)
   - Get confirmation and comment URL

8. **Confirm success**:
   - Display: "✓ Comment posted to PROJ-###"
   - Show Jira URL with issue link
   - Optionally: Add entry to WORKLOG noting comment was posted

## Related Commands

- `/comment "text"` - Add local WORKLOG entry (not Jira)
- `/import-issue PROJ-123` - Import Jira issue to local structure
- `/promote TASK-001` - Promote local issue to Jira (creates issue)
- `/plan PROJ-123` - Create implementation plan for Jira issue
- `/implement PROJ-123 1.1` - Execute work on Jira issue

## Notes

- **External issues only** - PROJ-###, not TASK-### or BUG-###
- **Always requires approval** - Never auto-posts
- **Context-aware** - Analyzes WORKLOG, commits, existing comments
- **Professional tone** - Assumes stakeholder audience
- **Complements /comment** - Jira updates vs local work logs
- **Respects privacy** - Only uses context you've already created locally
