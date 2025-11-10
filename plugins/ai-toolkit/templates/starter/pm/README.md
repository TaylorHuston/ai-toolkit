# Project Management Guide

## Overview

This directory contains all project management artifacts for planning and tracking work. The structure is template-driven, allowing customization while maintaining intelligent workflow assistance.

## Directory Structure

### Initial Structure

After `/toolkit-init`, the pm/ directory contains templates and placeholders:

```
pm/
├── README.md              # This guide
├── templates/             # Issue and spec templates
│   ├── README.md          # Template customization guide
│   ├── spec.md            # Epic template (structure + metadata)
│   ├── task.md            # Task template (structure + metadata)
│   ├── bug.md             # Bug template (structure + metadata)
│   └── resources/
│       └── .gitkeep
├── specs/                 # Feature specs (empty initially)
│   └── .gitkeep
└── issues/                # Tasks and bugs (empty initially)
    └── .gitkeep
```

### Active Project Structure

As you work, the AI creates specs and issues in organized locations:

```
pm/
├── README.md
├── templates/             # Templates remain for reference
│   ├── README.md
│   ├── spec.md
│   ├── task.md
│   └── bug.md
│
├── specs/                 # Created by /spec
│   ├── EPIC-001-user-authentication.md
│   ├── EPIC-002-data-management.md
│   ├── EPIC-003-admin-dashboard.md
│   └── EPIC-004-api-integration.md
│
└── issues/                # Created by /spec and /plan
    ├── TASK-001-user-registration/
    │   ├── TASK.md        # Definition, acceptance criteria, plan
    │   ├── WORKLOG.md     # Auto-created by /implement (reverse chronological)
    │   └── RESEARCH.md    # Optional technical deep dives
    │
    ├── TASK-002-login-flow/
    │   ├── TASK.md
    │   └── WORKLOG.md     # Created during implementation
    │
    ├── TASK-003-session-management/
    │   ├── TASK.md
    │   ├── PLAN.md        # Implementation phases (AI-managed)
    │   ├── WORKLOG.md
    │   └── RESEARCH.md    # Complex caching decision needed deep analysis
    │
    ├── TASK-004-password-reset/
    │   ├── TASK.md        # Requirements (PM tool synced)
    │   └── PLAN.md        # Implementation phases (not started yet)
    │
    └── BUG-001-session-timeout/
        ├── BUG.md         # Bug report (PM tool synced)
        ├── PLAN.md        # Fix plan (AI-managed)
        ├── WORKLOG.md     # Fix implementation history
        └── RESEARCH.md    # Root cause analysis
```

**File presence indicates progress:**
- **TASK.md + PLAN.md only**: Planned but not started
- **+ WORKLOG.md**: Implementation in progress or completed
- **+ RESEARCH.md**: Complex technical decisions documented

## Core Workflow

### 1. Define Strategy
```bash
/project-brief    # Create or update project vision
```

### 2. Create Epics
```bash
/spec             # Create new spec or refine existing
```

The `/spec` command:
- Guides you through conversational spec creation
- Reads `templates/spec.md` for structure (name, description, DoD, dependencies, tasks)
- Creates `specs/EPIC-###-name.md`
- Allows iterative refinement by passing existing EPIC-###

### 3. Plan Implementation
```bash
/plan TASK-001    # Add implementation plan to task
/plan BUG-003     # Add fix plan to bug
```

The `/plan` command:
- Finds issue in `issues/` directory
- Reads corresponding template (`templates/task.md` or `templates/bug.md`)
- Loads spec context from issue frontmatter
- Generates phase-based breakdown with TDD encouragement
- Performs complexity analysis and suggests decomposition

### 4. Execute Work
```bash
/implement TASK-001 1.1    # Execute specific phase with specialized agents
```

## Issue Types

### Standard Types

**TASK** - Implementation work
- Description, Acceptance Criteria, Technical Notes, Plan
- Template: `templates/task.md`

**BUG** - Defects and fixes
- Description, Reproduction Steps, Expected vs Actual Behavior, Fix Plan
- Template: `templates/bug.md`

### Custom Types

Create custom issue types by adding templates to `templates/` directory. See `templates/README.md` for details.

Examples of custom types teams create:
- **SPIKE** - Research and investigation
- **RFC** - Request for Comments / design proposals
- **EXPERIMENT** - Proof of concept work
- **DEBT** - Technical debt remediation

## ID Numbering

- **Epics**: `EPIC-001`, `EPIC-002`, ... (sequential, global)
- **Tasks**: `TASK-001`, `TASK-002`, ... (sequential, global)
- **Bugs**: `BUG-001`, `BUG-002`, ... (sequential, global)
- **Custom**: `[TYPE]-001`, `[TYPE]-002`, ... (sequential per type)

Each issue type maintains its own sequential numbering across the entire project.

## Workflow Integration

Commands automatically reference this structure:
- `/spec` reads `templates/spec.md` for required sections
- `/plan` reads issue type template for structure
- `/implement` executes specific phases from issue plans
- `/adr` references `specs/` for context
- `/design` references `specs/` for strategic alignment

## File Organization

### Epic Files
- Location: `specs/EPIC-###-kebab-case-name.md`
- Single file per spec
- Contains metadata (frontmatter), description, DoD, dependencies, task list

### Issue Files

Each issue gets a directory containing:

**[TYPE].md** (Required)
- Primary issue file with definition and plan
- Contains metadata (frontmatter), sections defined by template
- Includes acceptance criteria and phase-based breakdown

**WORKLOG.md** (Auto-created)
- Narrative work history created automatically by `/implement` after each phase
- Reverse chronological order (newest entries first)
- Documents: what was done, lessons learned, gotchas, files changed
- Format and guidelines: See `docs/development/guidelines/development-loop.md` (Work Documentation section)

**RESEARCH.md** (Optional)
- Deep technical investigations requiring multi-page analysis
- Created manually when complex decisions need detailed rationale
- Structure and criteria: See `docs/development/guidelines/development-loop.md` (Work Documentation section)

**File Relationship:**
- **[TYPE].md**: WHAT to do (plan checklist)
- **WORKLOG.md**: HOW it was done (narrative history with lessons)
- **RESEARCH.md**: WHY decisions were made (technical deep dives)

**Complete documentation standards**: `docs/development/guidelines/development-loop.md` contains comprehensive guidance on WORKLOG entry format, when to create RESEARCH.md, best practices, and examples.

## Customization

This system is template-driven. You can:
- Modify existing templates to change structure
- Add new templates for custom issue types
- Adjust section requirements and prompts
- Customize workflow to match your process

See `templates/README.md` for complete customization guide.

## Best Practices

### Epic Planning
- Keep specs focused (2-8 tasks typically)
- Write concrete Definition of Done to prevent scope creep
- Use flexible DoD format (prose or checklist) based on spec needs
- Reference ADRs and other specs for dependencies

### Task/Bug Creation
- Write testable acceptance criteria
- Reference spec in frontmatter (`spec: EPIC-001`)
- Use descriptive names (shows in file paths and git branches)
- Add technical notes for future reference

### Implementation Planning
- Run `/plan` before `/implement` to create phase-based breakdown
- Review phase breakdown and adjust if needed
- Follow TDD/BDD guidance from plan
- Keep plans living documents (update as you learn)

## Git Integration

The AI Toolkit uses a **three-branch workflow** with work branches aligned to issue IDs:

```bash
# Work branches created automatically by /implement or explicitly via /branch
feature/TASK-001    # Task implementation (TASK-001-user-registration)
feature/TASK-002    # Another task (TASK-002-login-flow)
bugfix/BUG-001      # Bug fix (BUG-001-session-timeout)
```

**Branch creation:**
```bash
# Method 1: Automatic (recommended)
/implement TASK-001 1.1
# → Prompts to create feature/TASK-001 if needed

# Method 2: Explicit
/branch create TASK-001
# → Creates feature/TASK-001 from develop
```

**Workflow:**
```bash
# Work on task
/implement TASK-001 1.1
/commit "implement user registration logic"

# Merge to staging (runs tests)
/branch merge develop

# Promote to production
/branch switch develop
/branch merge main

# Clean up
/branch delete feature/TASK-001
```

See `docs/development/guidelines/git-workflow.md` for complete three-branch workflow specification.

## Related Documentation

- **Templates**: `templates/README.md` - Template customization guide
- **Commands**: Plugin commands documentation for detailed command reference
- **Git Workflow**: Project git workflow guidelines
- **Project Brief**: `docs/project-brief.md` - Strategic context
