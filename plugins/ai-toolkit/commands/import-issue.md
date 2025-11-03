---
tags: ["jira", "integration", "import", "workflow"]
description: "Import Jira issue for local work with PLAN.md creation"
argument-hint: "PROJ-###"
allowed-tools: ["Read", "Write", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
---

# /import-issue Command

Import a Jira issue into local project structure for AI-assisted implementation.

## Purpose

Pull a Jira issue (created by PM or stakeholders) into the local repository to:
- Create local directory for implementation artifacts
- Display issue requirements from Jira
- Enable `/plan` and `/implement` workflows

**This is NOT required** - `/plan PROJ-123` will auto-import if needed. Use `/import-issue` when you want to:
- Preview issue details before planning
- Verify issue exists and you have access
- Explicitly pull issue into local structure

## Usage

```bash
/import-issue PROJ-123    # Import specific Jira issue
```

## Requirements

- **Jira integration enabled** in CLAUDE.md
- **Atlassian Remote MCP** configured
- **Issue must exist** in Jira and be accessible

## What It Does

1. **Fetches issue from Jira**:
   - Issue key, summary, description
   - Acceptance criteria (if present)
   - Issue type, status, epic link
   - Reporter, assignee
   - **If Epic**: Fetches all child issues in the epic

2. **Creates local directory**:
   - `pm/issues/PROJ-123-{kebab-case-summary}/`
   - No TASK.md created (Jira is source of truth)
   - **If Epic**: Creates `pm/epics/PROJ-123-{kebab-case-name}/` instead

3. **Displays issue details**:
   - Shows all fetched information
   - Provides Jira URL
   - **If Epic**: Lists all child issues in the epic
   - Suggests next steps

4. **Bulk import (Epics only)**:
   - Asks: "Import all issues in this epic?"
   - If yes: Imports each child issue (creates directories, displays summary)
   - Skips already-imported issues
   - Shows completion summary

5. **Ready for work**:
   - `/plan PROJ-123` - Create implementation plan
   - `/implement PROJ-123 1.1` - Start work

## Workflow Examples

### Example 1: Import Single Issue

```bash
# PM creates PROJ-456 in Jira
# Dev imports it

/import-issue PROJ-456

AI: Fetching PROJ-456 from Jira...

    ✓ Found: API Rate Limiting Implementation

    Type: Story
    Status: To Do
    Epic: PROJ-400 (API Infrastructure)
    Reporter: Sarah Chen

    Description:
    Implement rate limiting for REST API to prevent abuse.
    - 100 requests per minute per user
    - 1000 requests per minute per IP
    - Return 429 Too Many Requests with Retry-After header

    Acceptance Criteria:
    - [ ] Rate limiting middleware implemented
    - [ ] Redis backend for distributed counting
    - [ ] Proper HTTP 429 responses
    - [ ] Tests with 95%+ coverage

    ✓ Created pm/issues/PROJ-456-api-rate-limiting/

    Next steps:
    - Plan: /plan PROJ-456
    - View in Jira: https://company.atlassian.net/browse/PROJ-456
```

### Example 2: Import Epic with Bulk Child Import

```bash
/import-issue PROJ-400

AI: Fetching PROJ-400 from Jira...

    ✓ Found: API Infrastructure

    Type: Epic
    Status: In Progress
    Reporter: Tech Lead

    Description:
    Build robust API infrastructure with rate limiting, caching,
    and monitoring capabilities.

    Child Issues (5):
    - PROJ-456: API Rate Limiting Implementation (Story, To Do)
    - PROJ-457: Redis Caching Layer (Story, To Do)
    - PROJ-458: API Request Logging (Task, To Do)
    - PROJ-459: Performance Monitoring (Story, In Progress)
    - PROJ-460: API Documentation Generator (Task, To Do)

    ✓ Created pm/epics/PROJ-400-api-infrastructure/

    Import all 5 child issues? (yes/no)

User: yes

AI: Importing child issues...

    ✓ PROJ-456: API Rate Limiting Implementation
      → pm/issues/PROJ-456-api-rate-limiting/

    ✓ PROJ-457: Redis Caching Layer
      → pm/issues/PROJ-457-redis-caching-layer/

    ✓ PROJ-458: API Request Logging
      → pm/issues/PROJ-458-api-request-logging/

    ⊘ PROJ-459: Performance Monitoring (already imported)

    ✓ PROJ-460: API Documentation Generator
      → pm/issues/PROJ-460-api-documentation-generator/

    Summary:
    - Epic: PROJ-400 imported
    - Child issues: 4 new, 1 skipped (already imported), 5 total

    Next steps:
    - Plan any issue: /plan PROJ-456
    - View epic in Jira: https://company.atlassian.net/browse/PROJ-400
```

## Command Behavior

### Prerequisites Check

1. **Validate Jira enabled**:
   - Read CLAUDE.md
   - If `jira.enabled: false`: Error "Jira integration not enabled"

2. **Validate MCP**:
   - Check for Atlassian MCP tools
   - If unavailable: Error with setup instructions

3. **Validate ID format**:
   - Must match `[A-Z]+-###` pattern
   - Example: PROJ-123, ENG-456, BACKEND-789

### Fetch from Jira

**Use Atlassian MCP to get issue:**
- Issue key, summary, description
- Type (Story, Task, Bug, Epic)
- Status, priority
- Epic link (if part of epic)
- Reporter, assignee, created/updated dates
- Any custom fields visible to user
- **If type is Epic**: Fetch all child issues (stories, tasks, bugs in this epic)

### Create Local Directory

**For regular issues (Story, Task, Bug):**
```
pm/issues/PROJ-456-api-rate-limiting/
└── (empty - ready for PLAN.md, WORKLOG.md, RESEARCH.md)
```

**For epics:**
```
pm/epics/PROJ-400-api-infrastructure/
└── (empty - epic metadata, no local files needed since Jira is source)
```

**Naming:**
- Convert summary to kebab-case
- Strip special characters
- Truncate if too long (max 50 chars)

### Display Information

**For regular issues:**
```
Issue: PROJ-456
Summary: API Rate Limiting Implementation
Type: Story
Status: To Do
Epic: PROJ-400 (API Infrastructure)

Description:
[Full description from Jira]

Acceptance Criteria:
[Parsed from description or custom field]

Local directory: pm/issues/PROJ-456-api-rate-limiting/

Next steps:
- Create plan: /plan PROJ-456
- View in Jira: [URL]
```

**For epics:**
```
Epic: PROJ-400
Summary: API Infrastructure
Type: Epic
Status: In Progress

Description:
[Full description from Jira]

Child Issues (5):
- PROJ-456: API Rate Limiting Implementation (Story, To Do)
- PROJ-457: Redis Caching Layer (Story, To Do)
- PROJ-458: API Request Logging (Task, To Do)
- PROJ-459: Performance Monitoring (Story, In Progress)
- PROJ-460: API Documentation Generator (Task, To Do)

Local directory: pm/epics/PROJ-400-api-infrastructure/

Import all 5 child issues? (yes/no)
```

### Bulk Import Child Issues (Epics Only)

**When user confirms bulk import:**

1. **For each child issue**:
   - Fetch issue details from Jira
   - Check if already imported (directory exists)
   - Create local directory if new
   - Display progress

2. **Show summary**:
   - Count of new imports
   - Count of skipped (already imported)
   - Total child issues
   - Next step suggestions

3. **Error handling**:
   - Skip issues user can't access
   - Continue on individual failures
   - Report any issues at end

## Error Handling

### Issue Not Found
```
Error: PROJ-456 not found in Jira.

Possible causes:
- Issue doesn't exist
- No permission to view
- Wrong project key in CLAUDE.md

Check: https://company.atlassian.net/browse/PROJ-456
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

Or work locally: /plan TASK-001
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

### Directory Already Exists
```
Warning: pm/issues/PROJ-456-api-rate-limiting/ already exists.

This issue was previously imported. Contents:
- PLAN.md: ✓ exists
- WORKLOG.md: ✓ exists

Issue details (refreshed from Jira):
[Show latest Jira data]

Continue with: /plan PROJ-456 or /implement PROJ-456 1.1
```

## Integration with Workflow

**Position:** After issue created in Jira, before local work

```
Jira (PM creates) → /import-issue PROJ-456 → /plan PROJ-456 → /implement PROJ-456 1.1
```

**Alternative (auto-import):**
```
Jira (PM creates) → /plan PROJ-456 (auto-imports) → /implement PROJ-456 1.1
```

**Use /import-issue when:**
- You want to preview issue before planning
- You're unsure if issue exists
- You want to verify access permissions
- You want to see acceptance criteria before committing to work

**Skip /import-issue when:**
- You're ready to plan immediately: `/plan PROJ-456` auto-imports
- You trust the issue exists and is accessible

## Implementation Notes

When implementing `/import-issue PROJ-###`:

1. **Validate prerequisites**:
   - Read CLAUDE.md: Check `jira.enabled: true`
   - Check MCP: Ensure Atlassian MCP tools available
   - Validate ID: Must match `[A-Z]+-###` pattern

2. **Fetch from Jira**:
   - Use Atlassian MCP to get issue details
   - Parse description for acceptance criteria
   - Get epic link if exists
   - **If issue type is Epic**: Fetch all child issues in the epic
   - Handle "not found" gracefully

3. **Create directory**:
   - Convert summary to kebab-case
   - **If regular issue**: Create `pm/issues/PROJ-###-{name}/`
   - **If epic**: Create `pm/epics/PROJ-###-{name}/`
   - If exists: Show warning, display contents

4. **Display information**:
   - Show issue details clearly
   - Include Jira URL
   - **If epic**: List all child issues with types and statuses
   - Suggest next steps
   - Note: No TASK.md created (Jira is source)

5. **Bulk import prompt (epics only)**:
   - Ask: "Import all N child issues? (yes/no)"
   - If yes: Import each child issue sequentially
   - Show progress for each import
   - Skip already-imported issues
   - Display summary at end

6. **Return success**:
   - Confirm directory created
   - Ready for `/plan` or `/implement`

## Related Commands

- `/plan PROJ-123` - Create implementation plan (auto-imports if needed)
- `/implement PROJ-123 1.1` - Execute phase
- `/project-status` - View all imported issues
- `/promote TASK-001` - Convert local issue to Jira (opposite direction)

## Notes

- **Not required for workflow** - `/plan` auto-imports
- **Useful for exploration** - Preview before committing
- **Idempotent** - Can run multiple times (refreshes data)
- **Read-only** - Doesn't modify Jira, only fetches
