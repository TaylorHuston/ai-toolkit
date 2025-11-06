---
tags: ["workflow", "epic", "project-management", "conversational"]
description: "Create new epics or refine existing ones through natural language conversation"
argument-hint: "[EPIC-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/guidelines/issue-management.md  # Epic/issue file formats and naming conventions
  - docs/development/guidelines/pm-guidelines.md  # Epic creation workflow, Jira integration
---

# /epic Command

**WHAT**: Create/refine feature epics through conversational interaction.

**WHY**: Natural conversation ensures complete epic structure without rigid forms.

**HOW**: See issue-management.md for epic file format and naming conventions. See pm-guidelines.md for workflow patterns, Jira integration, and task suggestion.

## Usage

```bash
/epic                 # Start conversation (create new or work with existing)
/epic EPIC-###        # Work with specific epic
```

## Template System

**Epic location**: `pm/epics/EPIC-###-<name>.md` (local mode) OR Jira (PROJ-###)
**Issue location**: `pm/issues/TASK-###-<name>/` and `pm/issues/BUG-###-<name>/`

See pm/README.md for patterns and pm/templates/epic.md for structure.

## Execution Steps

### 1. Check Jira Mode - CRITICAL FIRST STEP

```bash
Read: CLAUDE.md  # Look for "## Jira Integration"
# Parse: Enabled: true OR Enabled: false
```

**Display mode confirmation:**
- If `Enabled: true`: "Creating epic in Jira (PROJ project)..."
- If `Enabled: false` or missing: "Creating local epic (pm/epics/)..."

**REMINDER**: Jira enabled = epics in Jira ONLY, no local pm/epics/ files.

### 2. Determine Intent

- No arguments: Ask "Create new or work on existing?"
- EPIC-### or PROJ-### provided: Load and enter refinement mode

### 3. Load Context

**Local mode:**
```bash
Read: pm/templates/epic.md  # Required sections
Glob: pm/epics/EPIC-*.md    # Determine next number
```

**Jira mode:**
```bash
Read: .ai-toolkit/jira-field-cache.json  # Field requirements
# Skip local file scanning
```

### 4. Creation Flow (Conversational)

**Following pm-guidelines.md epic structure:**

Ask user:
- What's the main goal?
- Who are the primary users?
- What are the success criteria?
- What's OUT of scope?

**Local mode:**
```bash
Write: pm/epics/EPIC-###-<name>.md
```

**Jira mode:**
```bash
# Collect fields conversationally (per pm-guidelines.md)
# Create via Atlassian MCP
# Get PROJ-### ID
# Display Jira URL
```

### 5. Optionally Add Initial Tasks

**Following pm-guidelines.md task suggestion strategy:**

Suggest tasks based on epic scope:
1. TASK-001-user-registration (user-story)
2. TASK-002-database-schema (task)
3. TASK-003-login-form (user-story)

Interactive loop: Which to create? (1/2/3/custom/stop)

### 6. Refinement Flow

**For existing epics:**
```bash
# Read epic and related issues
# Conversational: add tasks, update scope, check status
# Update epic file (local) or Jira (Jira mode)
```

## Agent Coordination

**Primary**: project-manager (conversation, creation)

**Supporting**:
- test-engineer (test strategy)
- Domain specialists (complexity)
- security-auditor (flags auth, encryption, PII, API work)

## Jira Integration

**Configuration** (from CLAUDE.md):
```yaml
jira:
  enabled: true/false
  project_key: PROJ
```

**Local Mode** (enabled: false):
- Epics: `pm/epics/EPIC-###-name.md`
- Issues: `pm/issues/TASK-###/`, `BUG-###/`
- Fully offline

**Jira Mode** (enabled: true):
- Epics: Jira ONLY (PROJ-100, PROJ-200)
- No local epic files
- Issues: Hybrid (Jira PROJ-### or local TASK-###/BUG-###)

**All Jira patterns in pm-guidelines.md:**
- Field discovery/caching
- Conversational field collection
- MCP creation
- Error handling

## Example Output

```
Creating epic in Jira (PROJ project)...

What's the main goal? → User authentication
Who are users? → End users
Success criteria? → Secure login, 95% coverage
OUT of scope? → No SSO, no 2FA yet

✓ Created PROJ-100: User Authentication
🔗 https://company.atlassian.net/browse/PROJ-100

Add initial tasks? (yes/no) → yes
✓ PROJ-101: User Registration
✓ PROJ-102: Login Flow

Next: /plan PROJ-101
```

## Integration

```
/project-brief → /epic → /plan TASK-### → /implement
                   ↓
              /epic EPIC-### (refine)
```

## Error Handling

**MCP unavailable (Jira mode):**
```
Error: Jira enabled but MCP not configured
Fix: Install MCP or set jira.enabled: false
```

**Field discovery fails:**
```
Warning: Using standard fields only
If creation fails, run: /refresh-schema
```

## Related Commands

- `/project-brief` - Project vision before epics
- `/plan TASK-###` - Implementation breakdown
- `/implement TASK-### PHASE` - Execute phases
- `/project-status` - Progress across epics

## Related Guidelines

- `docs/development/guidelines/issue-management.md` - Epic/issue file formats and naming
- `docs/development/guidelines/pm-guidelines.md` - Epic creation workflow and Jira integration
- `pm/README.md` - PM directory structure guide
- `pm/templates/epic.md` - Epic structure template
