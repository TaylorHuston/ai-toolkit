---
tags: ["workflow", "epic", "project-management", "conversational"]
description: "Create new epics or refine existing ones through natural language conversation"
argument-hint: "[EPIC-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/guidelines/pm-guidelines.md  # Epic/issue patterns, naming, Jira integration
---

# /epic Command

Work with feature epics through natural, conversational interaction. Create new epics or refine existing ones - the command adapts to what you need.

**Philosophy**: Natural language conversation on the surface, lightweight structured templates underneath to ensure completeness.

## Usage

```bash
/epic                 # Start conversation (create new or work with existing)
/epic EPIC-###        # Work with specific epic
```

## Template System

The command reads `pm/templates/epic.md` to understand required structure:
- Epic name, description, definition of done, dependencies, tasks
- YAML frontmatter configuration
- Section-specific prompts and requirements

**Epic Location**: `pm/epics/EPIC-###-<kebab-case-name>.md` (sequential numbering)
**Issue Location**: `pm/issues/TASK-###-<name>/` and `pm/issues/BUG-###-<name>/`

**See `pm/README.md`** for epic patterns, best practices, file organization, and ID numbering.

## Operations

### Create Epic

1. Determine next EPIC-### number (scan `pm/epics/`)
2. Read `pm/templates/epic.md` for required sections
3. Conversational Q&A to gather epic details
4. Create `pm/epics/EPIC-###-<name>.md`
5. Optionally add initial tasks (interactive loop)

### Refine Epic

1. Find epic file (Glob `pm/epics/EPIC-###-*.md`)
2. Read epic content and related issues
3. Conversational interaction to update scope, add tasks, review progress
4. Update epic file as needed

## Example Conversation

```
User: /epic

AI: Would you like to create a new epic or work on an existing one?

User: Create new

AI: [Reads pm/templates/epic.md for structure]

Let's create a new epic. Tell me about the feature or capability you want to build.
What's the main goal?

User: User authentication - login, signup, password reset, Google OAuth

AI: Great! I'll call this "User Authentication". Who are the primary users?

User: End users of our web app

AI: What are the key success criteria?

User: Users can create accounts, log in securely, reset passwords, 95%+ test coverage

AI: What's explicitly OUT of scope for this epic?

User: No SSO, no 2FA yet, only Google OAuth

AI: Creating EPIC-001-user-authentication...

✓ Epic created!

Would you like to add initial tasks? (yes/no)

User: yes

AI: Suggesting initial tasks based on epic scope...

1. TASK-001-user-registration (user-story)
2. TASK-002-database-schema (task)
3. TASK-003-login-form (user-story)

Which would you like to create? (1/2/3/custom/stop)

[Interactive loop continues until user says stop]

✓ Epic EPIC-001-user-authentication created
✓ 3 tasks created (TASK-001, TASK-002, TASK-003)

Next steps:
- Review epic: pm/epics/EPIC-001-user-authentication.md
- Plan implementation: /plan TASK-001
- Start development: /implement TASK-001 1.1
```

## Agent Coordination

**Primary**: `project-manager` - Leads conversation and creates epic/tasks

Invokes during issue creation:
- `test-engineer` - For test strategy
- Domain specialists - For complexity assessment
- `security-auditor` - Flags security-relevant work types (auth, encryption, PII, API integrations)

## Command Instructions

**All epic/issue patterns defined in** `pm-guidelines.md`:
- Epic structure, naming, numbering
- Issue types, templates, organization
- Jira integration patterns
- Task suggestion strategies

**This command orchestrates the conversation and reads guidelines for all rules.**

When this command is invoked:

1. **Check Jira Mode - CRITICAL FIRST STEP**
   - **ALWAYS** read CLAUDE.md to check Jira configuration BEFORE doing anything else
   - Look for "## Jira Integration" section
   - Parse the configuration block for `Enabled: true` or `Enabled: false`
   - **If Enabled: true**: Follow Jira creation flow (epics in Jira ONLY, no local pm/epics/ files)
   - **If Enabled: false** or section missing: Follow local creation flow (epics in pm/epics/)
   - **Reminder**: When Jira is enabled, epics MUST be created in Jira, not locally

2. **Display Mode Confirmation**
   - After reading Jira configuration, explicitly confirm to user:
   - If Jira enabled: "Creating epic in Jira (PROJ project)..."
   - If local mode: "Creating local epic (pm/epics/)..."
   - This prevents confusion about where epic will be created

3. **Determine Intent**
   - No arguments: Ask "Create new or work on existing?"
   - EPIC-### or PROJ-### provided: Load and enter refinement mode

4. **Load Context**
   - **Following** `pm-guidelines.md` **patterns**
   - Read pm/templates/epic.md for required sections (local mode only)
   - Scan for existing epics (determine next number - local mode only)
   - If Jira enabled: Check field cache, load Jira metadata, skip local file scanning

5. **Creation Flow**
   - **Following** `pm-guidelines.md` **epic structure**
   - Conversational Q&A for epic details
   - **If Jira mode**: Create epic in Jira via MCP, get PROJ-### ID, display Jira URL
   - **If local mode**: Create epic file in pm/epics/EPIC-###-name.md
   - Optionally suggest and create initial tasks
   - **Following** `pm-guidelines.md` **task suggestion strategy**

6. **Refinement Flow**
   - Read epic and related issues
   - Conversational interaction: add tasks, update scope, check status
   - Update epic file as needed (local) or Jira issue (Jira mode)

7. **Issue Creation** (when adding tasks)
   - **Following** `pm-guidelines.md` **issue patterns**
   - Determine next issue number (global scan)
   - Read appropriate template
   - Gather details conversationally
   - Create issue directory and file
   - Link to epic (frontmatter + task list)

## Jira Integration

**Hybrid Workflow**: Works with both local (EPIC-###) and Jira (PROJ-###) epics.

### Configuration

**Read CLAUDE.md:**
```yaml
jira:
  enabled: true/false
  project_key: PROJ
```

### Dual Mode Behavior

**Local Mode** (jira.enabled: false - default):
- Epics: `pm/epics/EPIC-###-name.md`
- Issues: `pm/issues/TASK-###/`, `BUG-###/`
- Fully offline

**Jira Mode** (jira.enabled: true):
- Epics: Created in Jira ONLY (PROJ-100, PROJ-200)
- No local epic files (`pm/epics/` empty)
- Issues: Hybrid - Jira (PROJ-###) or local (TASK-###, BUG-###)
- **Following** `pm-guidelines.md` **Jira patterns**

### Jira Creation Flow

**All Jira patterns defined in** `pm-guidelines.md`:
- Field discovery and caching (`.ai-toolkit/jira-field-cache.json`)
- Conversational field collection
- Epic/issue creation via Atlassian MCP
- Error handling (MCP unavailable, field discovery fails)

**Quick Flow:**
1. Check MCP availability
2. Load/discover required fields (with caching)
3. Conversational collection (standard + custom fields)
4. Create in Jira → Get PROJ-### ID
5. Optionally create initial issues
6. Display Jira URLs

### Example

```
User: /epic
AI: [Detects jira.enabled: true]
    Creating epic in Jira...
    ✓ Created PROJ-100: User Authentication
    🔗 https://company.atlassian.net/browse/PROJ-100

    Create initial issues? (yes/no)
User: yes
AI: ✓ PROJ-101: User Registration
    ✓ PROJ-102: Login Flow
    Next: /plan PROJ-101
```

### Error Handling

**MCP unavailable**:
```
Error: Jira enabled but Atlassian MCP not configured.
Fix: Install MCP or set jira.enabled: false
```

**Field discovery fails**:
```
Warning: Using standard fields only.
If epic creation fails, run: /refresh-schema
```

See `pm-guidelines.md` for complete Jira patterns and field caching details.

## Integration with Workflow

```
/project-brief → /epic (create) → /plan TASK-### → /implement TASK-### PHASE
                      ↓
                 /epic EPIC-### (refine iteratively)
```

**Workflow Relationships**:
- After `/project-brief` - Break down project vision into epics
- Before `/plan` - Define what to build before planning how
- Iterative refinement - Use `/epic EPIC-###` to update and add tasks
- Starts cycle - Epic is entry point for development work

## Related Commands

- `/project-brief` - Define project vision before creating epics
- `/epic EPIC-###` - Refine existing epic (add tasks, update scope)
- `/plan TASK-###` - Add implementation breakdown to tasks
- `/implement TASK-### PHASE` - Execute specific phases
- `/project-status` - Check progress across all epics

## Related Guidelines

- `docs/development/guidelines/pm-guidelines.md` - **Source of truth** for epic/issue patterns, Jira integration
- `pm/README.md` - User guide for PM directory structure and workflow
- `pm/templates/epic.md` - Epic structure and requirements
- `pm/templates/task.md` - Task structure and requirements
- `pm/templates/bug.md` - Bug structure and requirements
