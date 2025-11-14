---
tags: ["workflow", "epic", "jira", "project-management", "conversational"]
description: "Create Jira epics through natural language conversation (requires Jira integration)"
argument-hint: "[PROJ-### | --spec SPEC-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/guidelines/jira-integration.md  # Jira integration workflow
  - docs/development/guidelines/pm-guide.md  # Core PM workflows
---

# /epic Command

**WHAT**: Create/refine Jira epics through conversational interaction.

**WHY**: Natural conversation for creating PM tracking containers in Jira. Epics organize work at the PM tool level.

**HOW**: See jira-integration.md for Jira integration patterns. This command requires Jira integration enabled in CLAUDE.md.

## Usage

```bash
/epic                 # Create new Jira epic
/epic PROJ-###        # Work with specific Jira epic
/epic --spec SPEC-### # Create Jira epic from local feature spec
```

## Jira Integration Requirement

**This command requires Jira integration enabled.**

```yaml
## Jira Integration (in CLAUDE.md)
- **Enabled**: true
- **MCP Server**: Atlassian Remote MCP
- **Project Key**: PROJ
```

**If Jira is NOT enabled:**
- Command will error with setup instructions
- Use `/spec` for local feature specifications instead

## Local vs Jira Separation

**Feature Specs (use /spec):**
- Local files: `pm/specs/SPEC-###-name.md`
- Document WHAT to build
- Version controlled, always accessible

**Jira Epics (this command):**
- Jira only: PROJ-###
- PM tracking container
- Requires network/MCP access

**Semantic mapping:**
```
Local Spec (SPEC-001) = Specification (what to build)
Jira Epic (PROJ-100)  = Tracking container (PM tool)
```

## Execution Steps

### 1. Verify Jira Integration - CRITICAL FIRST STEP

```bash
Read: CLAUDE.md  # Look for "## Jira Integration"
# Check: Enabled: true
```

**If NOT enabled:**
```
Error: This command requires Jira integration

To create local feature specs, use:
  /spec

To enable Jira integration, see:
  docs/jira-integration.md
```

### 2. Check for --spec Flag

```bash
# If --spec SPEC-### provided:
#   1. Read pm/specs/SPEC-###-*.md
#   2. Extract: Name, Description, Definition of Done, Tasks
#   3. Pre-populate Jira epic fields
#   4. Continue to creation flow
```

**Display**:
- With `--spec`: "Creating Jira epic from SPEC-###..."
- Without flag: "Creating Jira epic in PROJ project..."

### 3. Determine Intent

- No arguments: Create new epic
- PROJ-### provided: Load and enter refinement mode
- --spec SPEC-###: Read spec and pre-populate

### 4. Load Context

**Jira mode:**
```bash
Read: .ai-toolkit/jira-field-cache.json  # Field requirements
# Determine required/optional fields
```

**If --spec flag:**
```bash
Read: pm/specs/SPEC-###-*.md
# Extract content for pre-population
```

### 5. Creation Flow (Conversational)

**Following jira-integration.md Jira epic structure:**

Ask user for required fields:
- Summary (epic name)
- Description
- Epic name (if different from summary)
- Custom fields (as discovered)

**Create in Jira:**
```bash
# Collect fields conversationally (per jira-integration.md)
# Create via Atlassian MCP
# Get PROJ-### ID
# Display Jira URL
```

**If created from --spec:**
```
Based on local spec SPEC-001:
- Summary: User Authentication
- Description: [Pre-populated from SPEC-001]
- Epic Name: user-authentication

Customize or accept? [Continue to conversational refinement]
```

### 6. Optionally Add Initial Issues

**Following jira-integration.md task suggestion strategy:**

Suggest issues based on epic scope:
1. PROJ-101: User Registration (Story)
2. PROJ-102: Database Schema (Task)
3. PROJ-103: Login Form (Story)

Interactive loop: Which to create? (1/2/3/custom/stop)

### 7. Refinement Flow

**For existing epics:**
```bash
# Fetch epic via Atlassian MCP
# Display current state
# Conversational: add issues, update fields
# Update via MCP
```

### 8. Optional Spec Creation

After creating epic, ask:
```
Create local feature spec from this epic?
→ Yes: Run /spec --epic PROJ-100
→ No: Continue without local spec
```

## Agent Coordination

**Primary**: project-manager (conversation, creation, Jira interaction)

**Supporting**:
- Domain specialists (for field suggestions)
- test-engineer (for acceptance criteria structure)

## Workflow Patterns

```bash
# Pattern 1: Spec-first (recommended)
/spec                    # Create SPEC-001 locally
/epic --spec SPEC-001    # Create PROJ-100 from spec

# Pattern 2: Epic-first
/epic                    # Create PROJ-100 in Jira
/spec --epic PROJ-100    # Create SPEC-001 from epic

# Pattern 3: Epic-only (no local spec)
/epic                    # Create PROJ-100
/import-issue PROJ-101   # Work on issues directly
```

## Example Output

```
Creating Jira epic from SPEC-001...

Extracted from SPEC-001: User Authentication
- Description: Enable secure user authentication...
- Tasks: 3 tasks identified

Customize or proceed? → proceed

Summary: User Authentication
Epic Name: user-authentication
Description: [Pre-populated]

✓ Created PROJ-100: User Authentication
🔗 https://company.atlassian.net/browse/PROJ-100

Add initial issues? (yes/no) → yes
✓ PROJ-101: User Registration
✓ PROJ-102: Login Flow

Next: /import-issue PROJ-101
```

## Integration

```
/spec → /epic --spec SPEC-### → /import-issue PROJ-### → /plan → /implement
          ↓
      /epic PROJ-### (refine)
```

## Error Handling

**Jira not enabled:**
```
Error: Jira integration not enabled

Enable Jira in CLAUDE.md:
  ## Jira Integration
  - **Enabled**: true
  - **MCP Server**: Atlassian Remote MCP
  - **Project Key**: PROJ

Or use /spec for local feature specs
```

**MCP unavailable:**
```
Error: Jira enabled but MCP not configured
Fix: Install Atlassian Remote MCP or disable Jira
```

**--spec file not found:**
```
Error: SPEC-001 not found
Check: pm/specs/ directory
Verify: Spec number is correct
```

**Field discovery fails:**
```
Warning: Using standard fields only
If creation fails, run: /refresh-schema
```

## Related Commands

- `/spec` - Create local feature specifications
- `/import-issue PROJ-###` - Import Jira issue for local work
- `/promote TASK-###` - Create Jira issue from local task
- `/comment-issue PROJ-###` - Add AI comments to Jira issue
- `/refresh-schema` - Refresh Jira field cache

## Related Guidelines

- `docs/development/guidelines/jira-integration.md` - Jira integration workflow
- `docs/jira-integration.md` - Jira setup and configuration
- `pm/templates/spec.md` - Local spec structure (for --spec flag)
