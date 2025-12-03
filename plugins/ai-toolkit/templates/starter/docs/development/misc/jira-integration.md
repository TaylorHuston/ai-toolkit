---
last_updated: "2025-11-13"
description: "Jira integration patterns for dual-mode PM (local + external PM tool)"
---

# Jira Integration Guide

**Referenced by Commands:** `/spec`, `/epic`, `/import-issue`, `/promote`, `/comment-issue`, `/refresh-schema`

## Overview

This guide defines patterns for integrating with Jira as an external project management tool while maintaining local development workflows.

**Core Concept:** Jira mode enables **dual PM** - Jira for team visibility, local files for AI-driven implementation.

**When to use Jira mode:**
- Team uses Jira for project tracking
- Need centralized spec/issue management
- Want to leverage Jira workflows, dashboards, reporting

**When to use local mode (default):**
- Solo projects or small teams
- Prefer file-based PM (no external dependencies)
- Want full offline capability

---

## Configuration in CLAUDE.md

**Jira mode is DISABLED by default.** Teams must explicitly enable in `CLAUDE.md`:

```markdown
## Jira Integration

- **Enabled**: true  # Set to false or omit to disable
- **Project Key**: PROJ  # Your Jira project key
- **Instance URL**: https://your-company.atlassian.net  # Jira instance
```

**Configuration Detection:**
Commands MUST read `CLAUDE.md` and look for the "## Jira Integration" section before creating specs/issues.

**Algorithm:**
1. Read `CLAUDE.md`
2. Find "## Jira Integration" section
3. Parse `Enabled: true` or `Enabled: false`
4. Extract Project Key if enabled
5. **If Enabled: true** → Use Jira mode
6. **If Enabled: false or missing** → Use local mode

---

## Dual Mode Behavior

### Local Mode (Default - Jira Disabled)

**Specs:** `pm/specs/SPEC-###-name.md` files
**Issues:** `pm/issues/###-name/` directories (type from TASK.md, BUG.md, or SPIKE.md)
**Workflow:** Fully offline, file-based, no external dependencies

**File structure:**
```
pm/
├── specs/
│   ├── SPEC-001-user-auth.md
│   └── SPEC-002-payment.md
└── issues/
    ├── 001-registration/
    │   ├── TASK.md           # Type from file
    │   ├── PLAN.md
    │   └── WORKLOG.md
    └── 002-login-timeout/
        ├── BUG.md            # Type from file
        ├── PLAN.md
        └── WORKLOG.md
```

### Jira Mode (Enabled)

**Specs:** Created in Jira ONLY (PROJ-100, PROJ-200)
- **No local spec files** - `pm/specs/` remains empty
- Jira is source of truth for spec metadata

**Issues:** Hybrid approach
- **Jira issues:** PROJ-101, PROJ-102 (team-facing, synced with Jira)
- **Local exploration:** 001, 002 (AI-driven, type from file, can be promoted later)

**Local directories:**
```
pm/
├── specs/                    # EMPTY (specs live in Jira)
└── issues/
    ├── PROJ-101-registration/
    │   ├── PLAN.md          # AI-managed (local only)
    │   ├── WORKLOG.md       # AI-managed (local only)
    │   └── (no TASK.md - Jira is source of truth)
    └── 001-spike/           # Local exploration issue
        ├── SPIKE.md         # Type from file
        ├── PLAN.md
        └── WORKLOG.md
```

**Key Insight:** `PLAN.md` and `WORKLOG.md` are always local (AI-managed). Only `TASK.md/BUG.md` sync with Jira.

---

## Field Discovery & Schema Caching

### The Challenge

Jira has **dynamic, configurable field schemas** that vary per:
- Project
- Issue type
- Organization customizations

Required fields and allowed values are not known until runtime.

### Field Discovery Process

**When creating specs/issues in Jira:**

1. **Check Cache First**
   - Read `.ai-toolkit/jira-field-cache.json`
   - If fresh (< 7 days) → Use cached schema
   - If stale or missing → Fetch from Jira

2. **Fetch Field Metadata** (via Atlassian Remote MCP)
   ```
   GET /rest/api/3/issue/createmeta?projectKeys=PROJ&issuetypeNames=Epic
   → Returns: required fields, field types, allowed values
   ```

3. **Cache Schema**
   - Store in `.ai-toolkit/jira-field-cache.json`
   - Include timestamp for staleness detection
   - Cache expires after 7 days

4. **Parse Requirements**
   - Identify required fields
   - Extract allowed values (dropdowns, etc.)
   - Determine field types (string, number, array)

### Conversational Field Collection

**Standard Fields (always present):**
- Summary (spec/issue title)
- Description (markdown content)

**Custom Required Fields (project-specific):**
Commands prompt conversationally with field name + options.

**Example:**
```
AI: "Your Jira requires 'Team' field. Which team?"
    Options: Frontend, Backend, DevOps
User: "Backend"

AI: "Your Jira requires 'Priority' field. What priority?"
    Options: High, Medium, Low
User: "High"
```

**Field Mapping:**
- Local SPEC.md fields → Jira Epic fields
- Local TASK.md fields → Jira Story/Task fields
- Local BUG.md fields → Jira Bug fields

### Refreshing Schema Cache

**Command:** `/refresh-schema`

**When to use:**
- Jira admin changed required fields
- Added new custom fields
- Modified allowed values for dropdowns
- Encountering "field not recognized" errors

**How it works:**
1. Delete `.ai-toolkit/jira-field-cache.json`
2. Force fetch fresh schema from Jira
3. Cache new schema
4. Confirm to user

**Design Rationale:**
- **Trade-off:** Fast (cached) vs always fresh (API call)
- **Choice:** Fast by default, manual refresh when needed
- **Why:** Schema changes are infrequent (admin-driven), performance matters

---

## Spec Creation in Jira Mode

**Command:** `/spec` or `/epic` (Jira-specific alias)

**Process:**

1. **Detect Jira Mode**
   - Read `CLAUDE.md`
   - Confirm `Jira Integration > Enabled: true`
   - Extract Project Key

2. **Discover Fields**
   - Check cache or fetch Epic schema
   - Parse required fields + allowed values

3. **Conversational Gathering**
   - Collect standard fields (Summary, Description, Acceptance Scenarios, Definition of Done)
   - Prompt for custom required fields with options
   - Validate against schema

4. **Create in Jira**
   - Use Atlassian Remote MCP: `create_epic` or `create_issue`
   - Submit all fields
   - Get Epic ID: PROJ-100

5. **Display Confirmation**
   ```
   ✓ Created PROJ-100: User Authentication
   🔗 https://your-company.atlassian.net/browse/PROJ-100
   ```

**NO local file created for specs.** Jira is source of truth.

---

## Issue Creation in Jira Mode

**Command:** `/spec` (during task creation) or direct issue creation

**Same field discovery + collection pattern as specs**

**Issue Type Mapping:**
- User Story → Jira Story
- Task → Jira Task
- Bug → Jira Bug

**Process:**

1. Detect Jira mode
2. Discover fields for issue type
3. Collect fields conversationally
4. Create in Jira → Get PROJ-101
5. **Optionally create local directory:**
   ```
   pm/issues/PROJ-101-user-registration/
   ├── PLAN.md      # Created by /plan command later
   └── WORKLOG.md   # Created by /implement command later
   ```

**Important:** NO `TASK.md` file in Jira mode. Jira issue is source of truth for "WHAT to build". Local files only for "HOW to build" (PLAN.md) and "WHAT happened" (WORKLOG.md).

**Spec Linkage:**
- Jira: Epic Link field (PROJ-101 → PROJ-100)
- Local: Frontmatter reference in PLAN.md (if needed)

---

## Promotion Workflow (Local → Jira)

**Command:** `/jira-promote 001`

**Use Case:** Local exploration validated, ready for team visibility

**Process:**

### Step 1: Read Local Issue

```
pm/issues/001-spike-oauth/
├── TASK.md      # Read description, acceptance criteria (type from file)
├── PLAN.md      # Migrate to new location
└── WORKLOG.md   # Migrate to new location
```

### Step 2: Discover Jira Fields

- Check cache or fetch schema for Task issue type
- Parse required fields

### Step 3: Map Local → Jira

**Field Mapping:**
- TASK.md `name` → Jira `Summary`
- TASK.md `description` → Jira `Description`
- TASK.md `acceptance_criteria` → Jira `Acceptance Criteria` (custom field if exists)
- TASK.md `spec` → Jira `Epic Link` (if spec is in Jira)

**Prompt for missing required fields:**
```
AI: "Jira requires 'Team' field. Which team?"
User: "Backend"
```

### Step 4: Create in Jira

- Submit issue creation
- Get Jira ID: PROJ-123

### Step 5: Migrate Local Files

```
# Before
pm/issues/001-spike-oauth/
├── TASK.md
├── PLAN.md
└── WORKLOG.md

# After
pm/issues/PROJ-123-spike-oauth/
├── PLAN.md      # Migrated (updated frontmatter: issue_number: PROJ-123)
└── WORKLOG.md   # Migrated (updated references to PROJ-123)

# TASK.md deleted (Jira is now source of truth)
```

### Step 6: Update Git Branch (if exists)

**Command automatically detects and updates:**
```
# Before
feature/001

# After
feature/PROJ-123
```

**Git operations:**
```bash
git branch -m feature/001 feature/PROJ-123
git push origin --delete feature/001  # Delete old remote branch
git push -u origin feature/PROJ-123   # Push renamed branch
```

### Step 7: Confirm to User

```
✓ Promoted 001 to PROJ-123
🔗 https://your-company.atlassian.net/browse/PROJ-123

Migrated:
- PLAN.md (updated references)
- WORKLOG.md (updated references)
- Git branch: feature/001 → feature/PROJ-123

Next: /implement PROJ-123
```

**Optional Cleanup:** User can delete `pm/issues/001-spike-oauth/` directory if desired.

---

## Import Workflow (Jira → Local)

**Command:** `/import-issue PROJ-123`

**Use Case:** Existing Jira issue needs local PLAN.md for AI-driven implementation

**Process:**

1. **Fetch from Jira** (via Atlassian Remote MCP)
   - Get issue details: Summary, Description, Acceptance Criteria, Epic Link
   - Get issue type: Story, Task, Bug

2. **Create Local Directory**
   ```
   pm/issues/PROJ-123-user-registration/
   └── (ready for /plan and /implement)
   ```

3. **Run /plan Automatically**
   - Read Jira issue description
   - If Epic Link exists, fetch Epic for acceptance scenarios
   - Generate PLAN.md with phases and scenario coverage

4. **Confirm to User**
   ```
   ✓ Imported PROJ-123: User Registration
   ✓ Created PLAN.md with 3 phases

   Next: /implement PROJ-123 1.1
   ```

---

## Comment Sync (Optional)

**Command:** `/comment-issue PROJ-123`

**Use Case:** Add AI-generated comments to Jira issue based on work context

**Process:**

1. Analyze local WORKLOG.md
2. Identify key decisions, blockers, or progress notes
3. Generate summary comment
4. Post to Jira issue via MCP

**Example:**
```
# From WORKLOG.md
2025-01-15: Implemented JWT auth with httpOnly cookies
2025-01-15: Decided to use bcrypt for password hashing (security-auditor approved)
2025-01-15: Blocked: Need API key for email service

# Posted to PROJ-123
AI Implementation Update:
- ✓ JWT auth implemented with httpOnly cookies
- ✓ Password hashing: bcrypt (security approved)
- 🚧 Blocked: Awaiting email service API key
```

---

## Related Documentation

**Core PM Workflows:**
- `pm-guide.md` - Core workflows and plan execution (Jira-agnostic)

**Templates:**
- `templates/spec-template.md` - Spec template (used in local mode)
- `templates/task-template.md` - Task template (used in local mode)
- `templates/bug-template.md` - Bug template (used in local mode)
- `templates/plan-template.md` - Plan template (used in both modes)

**Commands:**
- `/spec` - Creates specs (local mode: file, Jira mode: Jira Epic)
- `/epic` - Jira-specific alias for `/spec` in Jira mode
- `/import-issue` - Fetch Jira issue and create local PLAN.md
- `/promote` - Migrate local issue to Jira
- `/comment-issue` - Add AI comments to Jira issue
- `/refresh-schema` - Force refresh Jira field schema cache

**Configuration:**
- `CLAUDE.md` - Project configuration (Jira integration settings)
