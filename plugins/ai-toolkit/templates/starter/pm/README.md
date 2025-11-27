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
│   ├── spike.md           # Spike template (technical exploration)
│   └── resources/
│       └── .gitkeep
├── specs/                 # Feature specs (empty initially)
│   └── .gitkeep
└── issues/                # Tasks, bugs, spikes (empty initially)
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
│   ├── bug.md
│   └── spike.md
│
├── specs/                 # Created by /spec
│   ├── EPIC-001-user-authentication.md
│   ├── EPIC-002-data-management.md
│   ├── EPIC-003-admin-dashboard.md
│   └── EPIC-004-api-integration.md
│
└── issues/                # Created by /issue, /spec, /plan
    ├── 001-user-registration/
    │   ├── TASK.md        # Type = task (determined by file)
    │   ├── PLAN.md        # Implementation phases (AI-managed)
    │   ├── WORKLOG.md     # Auto-created by /implement (reverse chronological)
    │   └── RESEARCH.md    # Optional technical deep dives
    │
    ├── 002-login-flow/
    │   ├── TASK.md        # Type = task
    │   └── WORKLOG.md     # Created during implementation
    │
    ├── 003-session-timeout/
    │   ├── BUG.md         # Type = bug (determined by file)
    │   ├── PLAN.md        # Fix plan (AI-managed)
    │   ├── WORKLOG.md     # Fix implementation history
    │   └── RESEARCH.md    # Root cause analysis
    │
    ├── 004-graphql-vs-rest/
    │   ├── SPIKE.md       # Type = spike (questions, time box)
    │   ├── PLAN-1.md      # Exploration plan for approach 1
    │   ├── PLAN-2.md      # Exploration plan for approach 2
    │   ├── WORKLOG-1.md   # Findings from exploration 1
    │   ├── WORKLOG-2.md   # Findings from exploration 2
    │   ├── SPIKE-SUMMARY.md  # Consolidated findings + recommendation
    │   └── prototype/     # Throwaway exploration code (optional)
    │
    └── PROJ-123-oauth-integration/
        └── TASK.md        # External Jira issue (type from file)
```

**File presence indicates progress:**
- **TASK.md/BUG.md + PLAN.md only**: Planned but not started
- **+ WORKLOG.md**: Implementation in progress or completed
- **+ RESEARCH.md**: Complex technical decisions documented
- **SPIKE.md + PLAN-N.md files**: Exploration in progress
- **+ SPIKE-SUMMARY.md**: Exploration complete with recommendation

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
- Reads `templates/spec-template.md` for structure (name, description, DoD, dependencies, tasks)
- Creates `specs/EPIC-###-name.md`
- Allows iterative refinement by passing existing EPIC-###

### 3. Plan Implementation
```bash
/plan 001    # Add implementation plan to task
/plan 003    # Add fix plan to bug (detects type from file)
```

The `/plan` command:
- Finds issue in `issues/` directory
- Detects type from which file exists (TASK.md, BUG.md, SPIKE.md)
- Reads corresponding template (`templates/task-template.md` or `templates/bug-template.md`)
- Loads spec context from issue frontmatter
- Generates phase-based breakdown with TDD encouragement
- Performs complexity analysis and suggests decomposition

### 4. Execute Work
```bash
/implement 001 1.1    # Execute specific phase with specialized agents
```

## Issue Types

### Standard Types

**TASK** - Implementation work
- Description, Acceptance Criteria, Technical Notes, Plan
- Template: `templates/task-template.md`

**BUG** - Defects and fixes
- Description, Reproduction Steps, Expected vs Actual Behavior, Fix Plan
- Template: `templates/bug-template.md`

### Custom Types

Create custom issue types by adding templates to `templates/` directory. See `templates/README.md` for details.

Examples of custom types teams create:
- **RFC** - Request for Comments / design proposals
- **EXPERIMENT** - Proof of concept work
- **DEBT** - Technical debt remediation
- **HOTFIX** - Emergency production fixes

## ID Numbering

- **Specs**: `SPEC-001`, `SPEC-002`, ... (sequential, specs have their own counter)
- **Issues**: `001`, `002`, `003`, ... (single numeric counter for ALL issue types)
  - Type determined by which file exists: TASK.md, BUG.md, or SPIKE.md
  - External issues (Jira): `PROJ-123` work seamlessly alongside numeric IDs

**Why single counter?** Enables seamless integration with external issue tracking systems (Jira, Linear, etc.) that use single ID schemes.

## Workflow Integration

Commands automatically reference this structure:
- `/spec` reads `templates/spec-template.md` for required sections
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
- Format and guidelines: See `docs/development/workflows/task-workflow.md` (Work Documentation section)

**RESEARCH.md** (Optional)
- Deep technical investigations requiring multi-page analysis
- Created manually when complex decisions need detailed rationale
- Structure and criteria: See `docs/development/workflows/task-workflow.md` (Work Documentation section)

**File Relationship:**
- **[TYPE].md**: WHAT to do (plan checklist)
- **WORKLOG.md**: HOW it was done (narrative history with lessons)
- **RESEARCH.md**: WHY decisions were made (technical deep dives)

**Complete documentation standards**: `docs/development/workflows/task-workflow.md` contains comprehensive guidance on WORKLOG entry format, when to create RESEARCH.md, best practices, and examples.

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
feature/001    # Task implementation (001-user-registration/)
feature/002    # Another task (002-login-flow/)
bugfix/003     # Bug fix (003-session-timeout/)
spike/004/plan-1  # Spike exploration (004-graphql-vs-rest/)
```

**Branch creation:**
```bash
# Method 1: Automatic (recommended)
/implement 001 1.1
# → Prompts to create feature/001 if needed

# Method 2: Explicit
/branch create 001
# → Creates feature/001 from develop
```

**Workflow:**
```bash
# Work on task
/implement 001 1.1
/commit "implement user registration logic"

# Merge to staging (runs tests)
/branch merge develop

# Promote to production
/branch switch develop
/branch merge main

# Clean up
/branch delete feature/001
```

See `docs/development/workflows/git-workflow.md` for complete three-branch workflow specification.

## Related Documentation

- **Templates**: `templates/README.md` - Template customization guide
- **Commands**: Plugin commands documentation for detailed command reference
- **Git Workflow**: Project git workflow guidelines
- **Project Brief**: `docs/project-brief.md` - Strategic context
