---
# === Metadata ===
template_type: "guideline"
version: "1.0.0"
created: "2025-11-02"
last_updated: "2025-11-02"
status: "Active"
target_audience: ["AI Assistants", "Project Managers"]
description: "Epic and issue management patterns for project planning and tracking"

# === Machine-readable Configuration (for AI agents) ===

# Epic Configuration
epic_numbering: "sequential_global"     # EPIC-001, EPIC-002 across project
epic_location: "pm/epics/"
epic_file_pattern: "EPIC-###-<kebab-case-name>.md"
epic_template: "pm/templates/epic.md"

# Issue Configuration
issue_numbering: "sequential_per_type"  # TASK-001, BUG-001 (global per type)
issue_location: "pm/issues/"
issue_directory_pattern: "[TYPE]-###-<kebab-case-name>/"

# Issue Types
issue_types:
  task:
    prefix: "TASK"
    template: "pm/templates/task.md"
    description: "Implementation work (features, enhancements)"

  bug:
    prefix: "BUG"
    template: "pm/templates/bug.md"
    description: "Defects and fixes"

  # Custom types can be added by teams
  # spike:
  #   prefix: "SPIKE"
  #   template: "pm/templates/spike.md"
  #   description: "Research and investigation"

# Epic File Structure
epic_frontmatter:
  required_fields:
    - epic_number    # EPIC-###
    - status         # planning, in_progress, completed, on_hold
    - created        # YYYY-MM-DD
    - updated        # YYYY-MM-DD
  optional_fields:
    - owner          # Team or person responsible
    - priority       # high, medium, low
    - target_date    # YYYY-MM-DD

epic_sections:
  required:
    - name           # Epic title (extracted from conversation)
    - description    # What is this epic about
    - definition_of_done  # Concrete completion criteria
    - tasks          # Checkbox list of associated issues
  optional:
    - dependencies   # ADRs, other epics, external factors
    - scope          # What's included/excluded
    - technical_notes # Implementation considerations

# Issue File Structure
issue_frontmatter:
  required_fields:
    - issue_number   # TASK-###, BUG-###, etc.
    - type           # task, bug, etc.
    - status         # todo, in_progress, completed, blocked
    - created        # YYYY-MM-DD
    - updated        # YYYY-MM-DD
  optional_fields:
    - epic           # EPIC-### (which epic this belongs to)
    - assignee       # Team member (if using)
    - priority       # high, medium, low
    - estimate       # story points or hours

# Epic Creation Patterns
epic_breakdown:
  recommended_size: "2-8 tasks"       # Sweet spot for focused delivery
  max_size_warning: 10                # Suggest decomposition above this
  min_size_note: "1 task epics okay for significant features"

# Issue Suggestion Patterns
task_suggestion_strategy:
  analyze_scope: true                 # Parse epic description for key features
  suggest_count: "2-3"                # Initial suggestions per epic
  suggest_foundational_first: true    # Database, auth, core logic first
  user_driven: true                   # User selects, customizes, or adds own

---

# PM Guidelines

**Referenced by Commands:** `/epic` for epic/issue creation patterns

## Quick Reference

This guideline defines patterns for creating and managing epics and issues. Commands like `/epic` and `/plan` read these configurations to ensure consistent project management structure.

## Epic and Issue File Formats

**For complete file format specifications**, see `issue-management.md` which documents:
- EPIC.md structure (YAML frontmatter, sections, Definition of Done patterns)
- TASK.md structure (acceptance criteria, technical notes)
- BUG.md structure (reproduction steps, environment details)
- Issue directory structure (TASK.md, PLAN.md, WORKLOG.md, RESEARCH.md)
- Epic and issue naming conventions
- Epic size guidelines and decomposition patterns
- Issue type detection patterns
- Custom issue type configuration

**Quick Reference:**
- **EPIC.md**: `pm/epics/EPIC-###-kebab-name.md` (feature-level initiatives)
- **TASK.md**: `pm/issues/TASK-###-kebab-name/TASK.md` (implementation work)
- **BUG.md**: `pm/issues/BUG-###-kebab-name/BUG.md` (defects and fixes)
- **File purposes**: TASK.md (WHAT), PLAN.md (HOW), WORKLOG.md (WHAT was done), RESEARCH.md (WHY)

**Recommended Epic Size**: 2-8 tasks (decompose above 10 tasks)

**Numbering**: Sequential global per type (TASK-001, TASK-002, BUG-001, BUG-002)

## Epic Creation Workflow

**Pattern Used by `/epic` Command**

### 1. Conversational Creation

The `/epic` command conducts natural conversation to gather:
- Epic name (extracted or asked)
- Description (what capability are we building)
- Definition of Done (completion criteria)
- Dependencies (ADRs, other epics, external factors)

**Philosophy:** Natural on surface, structured underneath.

### 2. Epic Number Assignment

**Logic:**
1. Scan `pm/epics/` directory
2. Find highest EPIC-### number
3. Increment by 1
4. Assign to new epic

**Example:**
```
Existing: EPIC-001, EPIC-002, EPIC-004 (EPIC-003 deleted)
Next: EPIC-005 (continues sequence, doesn't fill gaps)
```

### 3. Name Conversion

User input: "User Authentication System"
↓ Convert to kebab-case
File name: `EPIC-005-user-authentication-system.md`

### 4. Task Suggestion Strategy

**After epic creation, optionally suggest initial tasks:**

**Analysis Approach:**
1. Parse epic description for key features
2. Identify foundational components (database, auth, core logic)
3. Suggest 2-3 foundational tasks
4. User selects, customizes, or describes own

**Example:**
```
Epic: "User Authentication System"
Description: "Login, registration, password reset, Google OAuth"

Suggested Tasks:
1. TASK-###: User Registration (core capability)
2. TASK-###: Login Flow (core capability)
3. TASK-###: Database Schema for Users (foundational)

User can:
- Accept all
- Select specific ones
- Describe custom task
- Skip task creation
```

**Interactive Loop:**
```
AI: "Create these tasks? (yes/all/custom/stop)"
User: "custom"
AI: "Describe the task you want to create..."
User: "Add password strength validation"
AI: "Creating TASK-###..."
AI: "Create another? (yes/no)"
```

## Issue Creation Workflow

**Pattern Used During Epic Task Creation**

### 1. Issue Number Assignment

**Logic (per issue type):**
1. Scan `pm/issues/` for all `[TYPE]-###-*` directories
2. Find highest number for this type
3. Increment by 1
4. Assign to new issue

**Important:** Numbering is GLOBAL per type, not per-epic.

**Example:**
```
Existing issues:
- TASK-001 (epic: EPIC-001)
- TASK-002 (epic: EPIC-001)
- TASK-003 (epic: EPIC-002)

Creating task for EPIC-003:
Next: TASK-004 (continues global sequence)
```

### 2. Issue Type Detection

**For bugs:** Always use BUG-### prefix
**For tasks:** Default to TASK-### prefix
**For custom:** Use custom prefix (SPIKE-###, RFC-###, etc.)

**Detection from conversation:**
- Keywords "bug", "fix", "broken", "regression" → BUG
- Keywords "feature", "add", "implement" → TASK
- Explicit type in request → Use that type

### 3. Template-Driven Creation

**Process:**
1. Determine issue type (TASK, BUG, etc.)
2. Read corresponding template (`pm/templates/[type].md`)
3. Parse template for required sections
4. Gather details conversationally
5. Create `pm/issues/[TYPE]-###-name/[TYPE].md` following template

**Template Sections (from templates):**
- YAML frontmatter (metadata)
- Description
- Acceptance Criteria
- Technical Notes (optional)
- NO Plan section (added later by `/plan`)

### 4. Epic Linkage

**Frontmatter Reference:**
```yaml
---
issue_number: TASK-###
type: task
epic: EPIC-001        # Links issue to epic
status: todo
created: YYYY-MM-DD
---
```

**Epic Update:**
```markdown
## Tasks
- [ ] TASK-001: User Registration
- [ ] TASK-002: Login Flow
- [ ] TASK-003: Password Reset  ← Added to epic file
```

**Bidirectional References:**
- Issue → Epic (via frontmatter `epic:` field)
- Epic → Issues (via Tasks section checkboxes)

## Jira Integration Patterns

**CRITICAL: Always check CLAUDE.md for Jira configuration before creating epics/issues**

### Detecting Jira Mode

**Commands MUST read CLAUDE.md and look for:**

```markdown
## Jira Integration
...
- **Enabled**: true  # or false
- **Project Key**: PROJ
```

**Configuration Detection:**
1. Read CLAUDE.md
2. Find "## Jira Integration" section
3. Parse the configuration for `Enabled: true` or `Enabled: false`
4. Extract Project Key if enabled
5. **If Enabled: true** → Use Jira mode
6. **If Enabled: false or missing** → Use local mode

**Reminder:** When Jira is enabled, epics MUST be created in Jira (not in pm/epics/)

### Dual Mode Behavior

**Local Mode (Jira Disabled - Default):**
- Epics: `pm/epics/EPIC-###-name.md` files
- Issues: `pm/issues/TASK-###/`, `BUG-###/` directories
- Fully offline, no external dependencies

**Jira Mode (Jira Enabled):**
- Epics: Created in Jira ONLY (PROJ-100, PROJ-200)
- No local epic files: `pm/epics/` remains empty
- Issues: Hybrid - Jira (PROJ-###) or local exploration (TASK-###, BUG-###)
- Local directories: `pm/issues/PROJ-###-name/` for PLAN.md, WORKLOG.md, RESEARCH.md

### Jira Epic Creation

**Field Discovery:**
1. Check cache: `.ai-toolkit/jira-field-cache.json`
2. If missing: Fetch field metadata from Jira
3. Parse required fields + allowed values
4. Cache for 7 days

**Conversational Collection:**
- Standard fields: Summary, Description
- Custom required fields: Prompt with field name + options
- Example: "Your Jira requires 'Team' field. Which team? (Frontend/Backend/DevOps)"

**Create in Jira:**
- Use Atlassian Remote MCP
- Get epic ID: PROJ-100
- Display Jira URL

### Jira Issue Creation

**Same field discovery + collection pattern as epics**

**Issue Type Mapping:**
- User Story → Jira Story
- Task → Jira Task
- Bug → Jira Bug

**Optional Local Directory:**
- Create `pm/issues/PROJ-###-name/` for:
  - `PLAN.md` - Implementation phases (AI-managed)
  - `WORKLOG.md` - Work history
  - `RESEARCH.md` - Technical deep dives
- NO `TASK.md` file (Jira is source of truth)

### Promotion Pattern

**Local → Jira Migration:**

When local exploration (TASK-###) is validated and ready for team:

1. `/promote TASK-001`
2. Read local TASK.md
3. Discover Jira fields
4. Create in Jira → Get PROJ-123
5. Migrate files:
   ```
   pm/issues/TASK-001-name/ → pm/issues/PROJ-123-name/
   ├── PLAN.md    (migrated)
   ├── WORKLOG.md (migrated)
   └── RESEARCH.md (migrated)
   ```
6. Update git branch: `feature/TASK-001` → `feature/PROJ-123`
7. Optional: Clean up original `TASK-001/` directory

See `/promote` command for detailed workflow.

## Naming Best Practices

### Epic Names

**Good Names** (clear capability focus):
- "User Authentication"
- "Payment Processing"
- "Admin Dashboard"
- "Search & Filtering"

**Avoid** (implementation details):
- "Add Login API Endpoints"
- "Build React Components"
- "Set Up Database"

### Task Names

**Good Names** (specific, actionable):
- "User Registration Form"
- "Password Reset Flow"
- "Email Verification System"
- "OAuth Integration"

**Avoid** (vague or overly technical):
- "User Stuff"
- "Fix Things"
- "Implement JWT Token Refresh Logic with Redis Cache"

**Sweet Spot:** Specific enough to understand, concise enough for file paths.

## Status Management

### Epic Status Values

- `planning` - Epic defined, tasks not yet created or planned
- `in_progress` - At least one task started
- `completed` - All tasks complete, DoD satisfied
- `on_hold` - Blocked or deprioritized

**Status Updates:**
- Manual updates by team
- Or: Infer from task status (all tasks complete → epic complete)

### Issue Status Values

- `todo` - Created, not started
- `in_progress` - Implementation underway
- `completed` - All acceptance criteria met
- `blocked` - Waiting on dependency or decision

**Status Updates:**
- `/implement` can update status automatically
- Or: Manual updates in issue frontmatter

## Integration with Development Workflow

**Epic/Issue Management Fits into Overall Workflow:**

```
1. /project-brief          → Define product vision
2. /epic                   → Break down into epics
3. /epic (refinement)      → Add tasks to epics
4. /plan TASK-###          → Create implementation plan
5. /implement TASK-### 1.1 → Execute phases
6. /commit                 → Create commits
7. /branch merge           → Merge to staging/production
```

**Key Relationships:**
- Epic → Tasks (one-to-many)
- Task → Plan (one-to-one, created by `/plan`)
- Task → Worklog (one-to-one, created by `/implement`)
- Epic → ADRs (many-to-many via Dependencies)

## Customization

### Adding Custom Issue Types

1. **Create Template:** `pm/templates/spike.md`
2. **Update Configuration:** Add to `issue_types` in YAML frontmatter above
3. **Use in Workflow:** `/epic` will automatically support new type

**Example Custom Type:**
```yaml
issue_types:
  spike:
    prefix: "SPIKE"
    template: "pm/templates/spike.md"
    description: "Time-boxed research and investigation"
```

### Modifying Epic Structure

1. **Update Template:** `pm/templates/epic.md`
2. **Update Configuration:** Modify `epic_sections` in YAML frontmatter
3. Commands adapt automatically

### Adjusting Recommended Sizes

**In YAML frontmatter above, modify:**
```yaml
epic_breakdown:
  recommended_size: "3-10 tasks"     # Your team's preference
  max_size_warning: 15               # Higher threshold
```

## Common Patterns

### Pattern: Feature Epic with Phased Delivery

```
EPIC-001: "Payment Processing"

Phase 1 (MVP):
- TASK-001: Credit Card Processing (Stripe)
- TASK-002: Basic Payment Form
- TASK-003: Receipt Generation

Phase 2 (Enhancements):
- TASK-004: PayPal Integration
- TASK-005: Saved Payment Methods
- TASK-006: Refund Processing
```

**Approach:** Create all tasks upfront, mark MVP tasks as higher priority.

### Pattern: Bug Fix Epic

```
EPIC-002: "Session Management Improvements"

- BUG-001: Session Timeout Not Working
- BUG-002: Session Cookies Not Secure
- TASK-007: Add Session Monitoring
- TASK-008: Session Cleanup Cron Job
```

**Approach:** Mix bugs and improvement tasks in single epic.

### Pattern: Spike Before Implementation

```
EPIC-003: "Real-time Notifications"

1. SPIKE-001: Research WebSocket vs SSE
2. SPIKE-002: Evaluate Pusher vs self-hosted
3. TASK-009: Implement chosen solution
```

**Approach:** Research tasks before implementation tasks.

## Related Documentation

**File Format Specifications:**
- `issue-management.md` - Complete EPIC.md, TASK.md, BUG.md file formats and naming conventions

**Project Management Structure:**
- `pm/README.md` - User guide for PM directory structure and workflow
- `pm/templates/README.md` - Template customization guide
- `pm/templates/epic.md` - Epic template (defines structure)
- `pm/templates/task.md` - Task template
- `pm/templates/bug.md` - Bug template

**Development Workflow:**
- `development-loop.md` - Implementation workflow and quality gates
- `plan-structure.md` - PLAN.md structure, phase patterns, progress tracking
- `worklog-format.md` - WORKLOG entry formats (standard and troubleshooting)
- `research-documentation.md` - RESEARCH.md format and criteria
- `git-workflow.md` - Branch naming aligned with issue IDs

**Commands:**
- `/epic` - Creates epics and issues following these patterns
- `/plan` - Reads issue files, creates PLAN.md
- `/implement` - Executes plans, creates WORKLOG.md
