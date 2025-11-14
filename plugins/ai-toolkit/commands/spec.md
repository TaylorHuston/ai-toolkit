---
tags: ["workflow", "spec", "feature-spec", "project-management", "conversational"]
description: "Create local feature specifications through natural language conversation"
argument-hint: "[SPEC-### | --epic PROJ-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/guidelines/pm-guide.md  # Spec creation workflows and file formats
  - docs/development/guidelines/jira-integration.md  # Jira mode (if enabled in CLAUDE.md)
---

# /spec Command

**WHAT**: Create/refine local feature specifications through conversational interaction.

**WHY**: Natural conversation ensures complete spec structure without rigid forms. Specs document WHAT to build locally, separate from PM tool tracking.

**HOW**: See pm-guide.md for spec creation workflows, file formats, and task suggestion strategies. For Jira mode, see jira-integration.md.

## Usage

```bash
/spec                 # Start conversation (create new or work with existing)
/spec SPEC-###        # Work with specific spec
/spec --epic PROJ-### # Create spec from Jira epic (requires Jira integration)
```

## Template System

**Spec location**: `pm/specs/SPEC-###-<name>.md` (always local)
**Issue location**: `pm/issues/TASK-###-<name>/` and `pm/issues/BUG-###-<name>/`

See pm/README.md for patterns and pm/templates/spec.md for structure.

## Execution Steps

### 1. Check for --epic Flag

```bash
# If --epic PROJ-### provided:
#   1. Verify Jira integration enabled (CLAUDE.md)
#   2. Fetch epic from Jira via Atlassian MCP
#   3. Extract: Summary → Name, Description, Acceptance Criteria
#   4. Pre-populate spec template
#   5. Continue to creation flow
```

**Display**:
- With `--epic`: "Creating spec from Jira epic PROJ-###..."
- Without flag: "Creating local feature spec..."

### 2. Determine Intent

- No arguments: Ask "Create new or work on existing?"
- SPEC-### provided: Load and enter refinement mode
- --epic PROJ-###: Fetch Jira epic and pre-populate

### 3. Load Context

**Always local:**
```bash
Read: pm/templates/spec.md  # Required sections
Glob: pm/specs/SPEC-*.md    # Determine next number
```

**If --epic flag:**
```bash
# Verify Jira enabled in CLAUDE.md
# Fetch PROJ-### via Atlassian MCP
# Extract fields for pre-population
```

### 4. Creation Flow (Conversational)

**Following spec template structure (pm/templates/spec.md):**

Ask user:
- What's the main goal?
- Who are the primary users?
- What are the acceptance scenarios? (2-5 key scenarios in Given-When-Then format)
  - Example: "Given user has valid credentials, When user submits login form, Then user is redirected to dashboard"
  - Focus on happy paths and critical edge cases
  - These scenarios become test phases in /plan
- What are the success criteria?
- What's OUT of scope?

**Create spec:**
```bash
Write: pm/specs/SPEC-###-<name>.md
```

**If created from --epic:**
```markdown
# SPEC-001: User Authentication

_Based on Jira epic PROJ-100_

[Pre-populated content from Jira]
```

### 5. Optionally Add Initial Tasks

**Following pm-guide.md task suggestion strategy:**

Suggest tasks based on spec scope:
1. TASK-001-user-registration (user-story)
2. TASK-002-database-schema (task)
3. TASK-003-login-form (user-story)

Interactive loop: Which to create? (1/2/3/custom/stop)

### 6. Refinement Flow

**For existing specs:**
```bash
# Read spec and related issues
# Conversational: add tasks, update scope, check status
# Update spec file
```

### 7. Optional Jira Epic Creation

After creating spec, ask:
```
Create corresponding Jira epic? (requires Jira integration)
→ Yes: Run /epic --spec SPEC-001
→ No: Continue with local spec only
```

## Agent Coordination

**Primary**: project-manager (conversation, creation)

**Supporting**:
- test-engineer (test strategy)
- Domain specialists (complexity)
- security-auditor (flags auth, encryption, PII, API work)

## Local vs Jira Separation

**Feature Specs (this command):**
- Always local: `pm/specs/SPEC-###-name.md`
- Document WHAT to build
- Can be created independently OR from Jira epic
- Version controlled with code

**Epics (use /epic command):**
- Jira only when integration enabled
- PM tracking container
- Created independently OR from feature spec

**Workflow patterns:**
```bash
# Pattern 1: Local-first
/spec                    # Create SPEC-001
/epic --spec SPEC-001    # Create PROJ-100 from spec

# Pattern 2: Jira-first
/spec --epic PROJ-100    # Create SPEC-001 from epic

# Pattern 3: Local-only
/spec                    # Create SPEC-001
# No Jira epic needed
```

## Example Output

```
Creating local feature spec...

What's the main goal? → User authentication
Who are users? → End users

Acceptance scenarios (2-5 key scenarios in Given-When-Then):
→ 1. Given user has valid credentials, When user submits login, Then redirected to dashboard
→ 2. Given user enters invalid password, When user submits, Then error shown and login blocked
→ 3. Given user is inactive, When user tries to login, Then account reactivation prompt shown

Success criteria? → Secure login, 95% test coverage, <2s response time
OUT of scope? → No SSO, no 2FA yet

✓ Created SPEC-001: User Authentication
📄 pm/specs/SPEC-001-user-authentication.md

Add initial tasks? (yes/no) → yes
✓ TASK-001: User Registration
✓ TASK-002: Login Flow

Create corresponding Jira epic? (requires Jira integration)
→ No

Next: /plan TASK-001
```

## Integration

```
/project-brief → /spec → /plan TASK-### → /implement
                   ↓
              /spec SPEC-### (refine)
                   ↓
              /epic --spec SPEC-### (create Jira epic)
```

## Error Handling

**--epic flag without Jira:**
```
Error: --epic requires Jira integration
Fix: Enable Jira in CLAUDE.md or omit --epic flag
```

**Epic not found:**
```
Error: Epic PROJ-100 not found in Jira
Verify: Epic exists and you have access
```

**MCP unavailable:**
```
Warning: Cannot fetch epic, Jira MCP unavailable
Fallback: Create spec manually
```

## Related Commands

- `/project-brief` - Project vision before specs
- `/epic` - Create Jira epics (optionally from spec)
- `/plan TASK-###` - Implementation breakdown
- `/implement TASK-### PHASE` - Execute phases
- `/project-status` - Progress across specs

## Related Guidelines

- `docs/development/guidelines/pm-guide.md` - Spec creation workflows and file formats
- `docs/development/guidelines/jira-integration.md` - Jira mode (if enabled)
- `pm/README.md` - PM directory structure guide
- `pm/templates/spec.md` - Spec structure template
