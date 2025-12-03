# {app-name}

{description}

## Quick Start

### Prerequisites

1. **Install AI Toolkit Plugin**:
   ```bash
   /plugin marketplace add TaylorHuston/ai-toolkit
   /plugin install ai-toolkit
   ```

2. **Customize Project Context**:
   - Edit `CLAUDE.md` with your tech stack and external links
   - Update this README with your project details

### Start Developing

```bash
# 1. Complete your project brief
/project-brief

# 2. Create your first spec
/spec

# 3. Make architecture decisions
/adr

# 4. Plan implementation
/plan 001

# 5. Execute tasks
/implement 001 1.1
```

### Keeping Templates Updated

When the AI Toolkit plugin is updated with improvements to guidelines, workflows, or templates:

```bash
# Preview changes without modifying files
/toolkit-init --dry-run

# Update templates while preserving your customizations
/toolkit-init

# Complete reset (overwrites all customizations)
/toolkit-init --force
```

**What gets updated:**
- New guideline files in `docs/development/`
- Improved workflow templates
- Bug fixes to templates
- New PM templates

**What's preserved:**
- Your customized guidelines (unless using --force)
- Your project-specific docs
- All PM files (specs, tasks, ADRs)
- CLAUDE.md, README.md customizations

**Best practice:** Run `/toolkit-init --dry-run` after plugin updates to see what's new.

## Project Structure

### Initial Structure (After /toolkit-init)

The starter template provides **50 files** organized for clarity:

```
your-project/
├── CLAUDE.md                      # Project context for AI (customize with your tech stack)
├── README.md                      # This file
├── GETTING-STARTED.md             # AI Toolkit usage guide
├── CHANGELOG.md                   # Version history
├── .gitignore                     # Standard ignores
│
├── docs/                          # Documentation hub
│   ├── README.md                  # Documentation guide
│   ├── project-brief.md           # Your project vision (start here!)
│   │
│   ├── project/                   # Project-specific docs (created by AI as you work)
│   │   ├── README.md              # Project docs guide
│   │   ├── architecture-overview.md    # High-level architecture (from template)
│   │   ├── writing-style.md            # Documentation writing standards (from template)
│   │   ├── adrs/                  # Architecture Decision Records
│   │   │   ├── README.md          # ADR guide
│   │   │   └── adr-template.md    # ADR template (instance, created from template)
│   │   ├── design-assets/         # Design files and assets
│   │   │   └── README.md          # Design assets guide
│   │   └── ui-designs/            # UI mockups and prototypes
│   │       ├── README.md          # UI designs guide
│   │       └── components/        # Component library mockups
│   │           └── README.md      # Components guide
│   │
│   └── development/               # Development guidelines and templates
│       ├── README.md              # Guidelines system overview
│       │
│       ├── conventions/           # Project conventions (7 files)
│       │   ├── api-guidelines.md
│       │   ├── architectural-principles.md
│       │   ├── coding-standards.md
│       │   ├── security-guidelines.md
│       │   ├── testing-standards.md
│       │   ├── ui-design-guidelines.md
│       │   └── versioning-and-releases.md
│       │
│       ├── workflows/             # AI execution protocols (9 files)
│       │   ├── agent-coordination.md
│       │   ├── bug-workflow.md
│       │   ├── git-workflow.md
│       │   ├── pm-file-formats.md
│       │   ├── pm-workflows.md
│       │   ├── quality-gates.md
│       │   ├── spike-workflow.md
│       │   ├── task-workflow.md
│       │   └── worklog-format.md
│       │
│       ├── templates/             # PM and doc templates (12 files)
│       │   ├── README.md
│       │   ├── adr-template.md
│       │   ├── architecture-overview-template.md
│       │   ├── bug-template.md
│       │   ├── data-model-template.md
│       │   ├── note-template.md
│       │   ├── plan-template.md
│       │   ├── project-brief-template.md
│       │   ├── spec-template.md
│       │   ├── spike-template.md
│       │   ├── task-template.md
│       │   └── writing-style-template.md
│       │
│       └── misc/                  # Reference docs and guides (4 files)
│           ├── commands.md        # Complete command reference
│           ├── agents.md          # Agent catalog
│           ├── optional-mcp-servers.md  # Optional integrations
│           └── jira-integration.md      # Jira setup guide
│
└── pm/                            # Project management
    ├── README.md                  # PM guide
    ├── specs/                     # Feature specs (created by /spec)
    │   └── .gitkeep
    └── issues/                    # Tasks and bugs (created by /spec and /plan)
        └── .gitkeep
```

**50 files breakdown:**
- **5 core files**: CLAUDE.md, README.md, GETTING-STARTED.md, CHANGELOG.md, .gitignore
- **45 structure files**: 16 guidelines (7 conventions + 9 workflows), 12 templates, 4 misc docs, 10 README files, 2 .gitkeep placeholders

### Active Project Structure (After Development)

As you work, the AI creates content in organized locations:

```
your-project/
├── docs/
│   ├── project-brief.md           # ✅ Filled in via /project-brief
│   │
│   ├── project/
│   │   ├── adrs/                  # Created by /adr
│   │   │   ├── ADR-001-database-selection.md
│   │   │   ├── ADR-002-authentication-strategy.md
│   │   │   └── ADR-003-api-architecture.md
│   │   │
│   │   └── design/                # Created during design phase
│   │       ├── system-architecture.md
│   │       └── api-specification.md
│   │
│   └── development/
│       ├── conventions/           # Customize as you make decisions
│       │   ├── api-guidelines.md            # Fill in via /adr decisions
│       │   ├── architectural-principles.md  # Fill in via /adr decisions
│       │   ├── coding-standards.md          # Fill in as conventions emerge
│       │   ├── security-guidelines.md       # Fill in via /security-audit
│       │   ├── testing-standards.md         # Define your testing approach
│       │   ├── ui-design-guidelines.md      # Design tokens and UI patterns
│       │   └── versioning-and-releases.md   # ✅ Pre-configured
│       │
│       ├── workflows/             # AI execution protocols
│       │   ├── task-workflow.md          # ✅ Pre-configured
│       │   ├── git-workflow.md              # ✅ Pre-configured (three-branch)
│       │   └── [other workflow files]       # Pre-configured
│       │
│       └── templates/             # PM and documentation templates
│           └── [template files]             # Used by AI commands
│
├── pm/
│   ├── specs/                     # Created by /spec
│   │   ├── EPIC-001-user-authentication.md
│   │   ├── EPIC-002-data-management.md
│   │   └── EPIC-003-admin-dashboard.md
│   │
│   └── issues/                    # Created by /issue and /plan
│       ├── 001-user-registration/
│       │   ├── TASK.md            # Type from file (task)
│       │   ├── WORKLOG.md         # Work history (auto-created by /implement)
│       │   └── RESEARCH.md        # Technical deep dives (optional)
│       │
│       ├── 002-login-flow/
│       │   ├── TASK.md            # Type from file (task)
│       │   └── WORKLOG.md
│       │
│       └── 003-session-timeout/
│           ├── BUG.md             # Type from file (bug)
│           ├── WORKLOG.md         # Fix history
│           └── RESEARCH.md        # Root cause analysis
│
└── src/                           # Your application code
    └── [your source files]
```

**Philosophy**: Structure emerges from work, not upfront planning. The AI creates documentation as you build, ensuring it reflects reality rather than aspirations.

## Available Commands

The AI Toolkit provides **26 commands** for structured development:

### Setup & Strategy
- `/toolkit-init` - Initialize project structure (you've already run this!)
- `/project-brief` - Create/update project vision through interactive Q&A

### Spec & Epic Management
- `/spec` - Create feature specs with acceptance criteria
- `/jira-epic` - Create epics in Jira (requires Jira integration)
- `/jira-import` - Import Jira issues for local work

### Planning & Implementation
- `/adr` - Make architecture decisions and create ADRs
- `/issue` - Create work items (TASK, BUG, or SPIKE) from natural language description
- `/plan ###` - Add implementation plan to tasks/bugs/spikes (auto-detects type)
- `/implement ### PHASE` - Execute specific phases with specialized agents
- `/advise ### PHASE` - Get implementation guidance without code generation
- `/complete ###` - Complete any issue type per its workflow requirements

### Quality & Security
- `/quality` - Multi-dimensional quality analysis (includes security via security-auditor agent)
- `/troubleshoot` - Structured 5-step troubleshooting workflow
- `/sanity-check` - Mid-work validation and alignment check

### Development Support
- `/branch` - Branch operations (create, merge, delete, switch, status)
- `/commit` - Branch-aware git commits with automatic issue references
- `/worklog` - Add timestamped work log entries
- `/sync-progress` - Sync project state after manual changes

### Documentation & Status
- `/docs` - Unified documentation management
- `/project-status` - Project status dashboard with intelligent context
- `/changelog` - Check and update CHANGELOG.md
- `/release` - Release new version following semantic versioning

### Utilities
- `/refresh` - Silently refresh AI context
- `/ui-design` - Create HTML UI mockups with parallel variants
- `/jira-comment` - Add AI-suggested comments to Jira issues
- `/jira-promote` - Promote local exploration issues to Jira

See `docs/development/misc/commands.md` for complete command reference with usage examples.

## Contributing

[Add your contribution guidelines]

## License

[Add your license]
