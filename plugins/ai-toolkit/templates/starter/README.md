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
/plan TASK-001

# 5. Execute tasks
/implement TASK-001 1.1
```

## Project Structure

### Initial Structure (After /toolkit-init)

The starter template provides **37 files** organized for clarity:

```
your-project/
├── CLAUDE.md                      # Project context for AI (customize with your tech stack)
├── README.md                      # This file
├── GETTING-STARTED.md             # AI Toolkit usage guide
├── .gitignore                     # Standard ignores
│
├── docs/                          # Documentation hub
│   ├── README.md                  # Documentation guide
│   ├── project-brief.md           # Your project vision (start here!)
│   │
│   ├── project/                   # Project-specific docs (created by AI as you work)
│   │   ├── README.md              # Project docs guide
│   │   ├── adrs/                  # Architecture Decision Records
│   │   │   └── README.md          # ADR guide
│   │   └── design/                # Design assets and specs
│   │       └── README.md          # Design docs guide
│   │
│   └── development/               # Development guidelines (customize as needed)
│       ├── README.md              # Guidelines system overview
│       └── guidelines/            # 8 customizable guideline templates
│           ├── development-loop.md          # AI-assisted workflow (quality gates, TDD)
│           ├── api-guidelines.md            # API patterns (TBD by default)
│           ├── testing-standards.md         # Testing approach (TBD by default)
│           ├── git-workflow.md              # Three-branch workflow (pre-configured)
│           ├── coding-standards.md          # Code style (TBD by default)
│           ├── security-guidelines.md       # Security practices (TBD by default)
│           └── architectural-principles.md  # Design philosophy (TBD by default)
│
└── pm/                            # Project management
    ├── README.md                  # PM guide
    │
    ├── specs/                     # Feature specs (created by /spec)
    │   └── .gitkeep
    │
    ├── issues/                    # Tasks and bugs (created by /spec and /plan)
    │   └── .gitkeep
    │
    └── templates/                 # Issue templates
        ├── README.md              # Template customization guide
        ├── spec.md                # Epic template
        ├── task.md                # Task template
        ├── bug.md                 # Bug template
        └── resources/
            └── .gitkeep
```

**37 files breakdown:**
- **9 core files**: CLAUDE.md, README.md, GETTING-STARTED.md, CHANGELOG.md, .gitignore, 5 top-level README files
- **28 structure files**: 8 guidelines, 5 templates (spec, task, bug, plan, adr), 9 documentation READMEs, 6 .gitkeep placeholders

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
│       └── guidelines/            # Customize as you make decisions
│           ├── development-loop.md          # ✅ Pre-configured
│           ├── api-guidelines.md            # Fill in via /adr decisions
│           ├── testing-standards.md         # Define your testing approach
│           ├── git-workflow.md              # ✅ Pre-configured (three-branch)
│           ├── coding-standards.md          # Fill in as conventions emerge
│           ├── security-guidelines.md       # Fill in via /security-audit
│           └── architectural-principles.md  # Fill in via /adr decisions
│
├── pm/
│   ├── specs/                     # Created by /spec
│   │   ├── EPIC-001-user-authentication.md
│   │   ├── EPIC-002-data-management.md
│   │   └── EPIC-003-admin-dashboard.md
│   │
│   └── issues/                    # Created by /spec and /plan
│       ├── TASK-001-user-registration/
│       │   ├── TASK.md            # Task definition with plan
│       │   ├── WORKLOG.md         # Work history (auto-created by /implement)
│       │   └── RESEARCH.md        # Technical deep dives (optional)
│       │
│       ├── TASK-002-login-flow/
│       │   ├── TASK.md
│       │   └── WORKLOG.md
│       │
│       └── BUG-001-session-timeout/
│           ├── BUG.md             # Bug report with fix plan
│           ├── WORKLOG.md         # Fix history
│           └── RESEARCH.md        # Root cause analysis
│
└── src/                           # Your application code
    └── [your source files]
```

**Philosophy**: Structure emerges from work, not upfront planning. The AI creates documentation as you build, ensuring it reflects reality rather than aspirations.

## Available Commands

The AI Toolkit provides **14 commands** for structured development:

### Setup & Strategy
- `/toolkit-init` - Initialize project structure (you've already run this!)
- `/project-brief` - Create/update project vision through interactive Q&A

### Epic & Planning
- `/spec` - Create new specs or refine existing ones
- `/adr` - Make architecture decisions and create ADRs

### Core Workflow
- `/plan TASK-###` - Add implementation plan to tasks/bugs
- `/implement TASK-### PHASE` - Execute specific phases with specialized agents

### Quality & Security
- `/quality` - Multi-dimensional quality analysis
- `/security-audit` - OWASP-compliant security assessment
- `/test-fix` - Automated test failure detection and resolution

### Development Support
- `/branch` - Branch operations (create, merge, delete, switch, status)
- `/commit` - Branch-aware git commits with automatic issue references
- `/comment` - Add timestamped work log entries

### Documentation & Status
- `/docs` - Unified documentation management
- `/project-status` - Project status dashboard with intelligent context

See `GETTING-STARTED.md` for detailed workflow guide and command usage examples.

## Contributing

[Add your contribution guidelines]

## License

[Add your license]
