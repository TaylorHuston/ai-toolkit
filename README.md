# AI Toolkit - Claude Plugin

A comprehensive AI-assisted development workflow system for Claude Code, providing intelligent task orchestration, specialized agent coordination, and file-based state management.

## IMPORTANT
This is very much an alpha/experiment at the moment. Look at the commit history to see that for yourself. Right now it's a lot of throwing lots of things at the wall, seeing what works, seeing what doesn't, and frequently changing things as I use it in more "real world" scenarios.

## What's Included

This marketplace contains:

- **AI Toolkit Plugin** - Complete workflow system with 24 commands, 21 specialized agents, and intelligent automation
- **Starter Template** - 49 essential files for clean project initialization via `/toolkit-init`
- **Development Guidelines** - 30+ customizable files organized in 4 directories (conventions, workflows, misc, templates)

## Quick Start

### Install and Initialize

```bash
# 1. Add this marketplace
/plugin marketplace add TaylorHuston/ai-toolkit

# 2. Install the AI Toolkit plugin
/plugin install ai-toolkit

# 3. Initialize your project
cd your-project
/toolkit-init

# 4. Start developing
/project-brief
```

The `/toolkit-init` command scaffolds your project with:
- Customized CLAUDE.md (your tech stack and links)
- Structured template (49 files: docs/, pm/, development guidelines, templates)
- GETTING-STARTED.md guide
- Documentation framework (AI creates content as you work)
- Interactive setup with smart conflict resolution

### Keeping Templates Updated

After plugin updates, sync your project with the latest templates:

```bash
# Preview changes without modifying files
/toolkit-init --dry-run

# Update templates while preserving your customizations
/toolkit-init

# Complete reset (overwrites all customizations - use carefully)
/toolkit-init --force
```

**What gets updated:**
- New guideline files in `docs/development/`
- Improved workflow templates
- Bug fixes to templates
- New PM templates

**What's preserved:**
- Your customized guidelines
- Your project-specific docs (ADRs, design assets)
- All PM files (specs, tasks, bugs, plans, worklogs)
- CLAUDE.md and README.md customizations

**Best practice:** Run `/toolkit-init --dry-run` after plugin updates to see what's new.

### Intended Workflow

1. **`/project-brief`** - Create your Project Brief through an interactive session. This is the high-level, relatively non-technical overview of your project: the problem you're solving, your target audience, core features. Think "One Pager" or "Elevator Pitch" - the guiding direction for everything else.

2. **`/adr`** - Create Architecture Decision Records for technical decisions ("What database should I use?", "Deploy to Vercel or Netlify?"). ADRs are intended to be static once approved - if something needs changing, create a new ADR to supersede it.

3. **`/spec`** - Create a Spec Document (the heart of the workflow). A Spec is a concrete plan for a body of related work - similar in scope to an Epic in Agile or a PRD. Written for easy parsing by Claude Code with clear requirements, acceptance criteria, and definition of done. Unlike ADRs, Specs are living documents that adapt as you discover hiccups and make adjustments during implementation.

4. **`/plan TASK-###`** - Create a thorough implementation plan with all steps needed to complete the task. In the default configuration, this breaks work into discrete Phases (each a logical commit with strict TDD workflow), but you can customize to your specifications.

5. **`/implement TASK-### PHASE`** - Start implementing. Execute a single phase or let Claude complete the entire task end-to-end. Quality gates and workflows make autonomous execution both possible and safe, but you're always in control.

> **Next Steps**: For technical architecture details, see [Core 3-Phase Workflow](#-core-3-phase-workflow). For file-based project management, see [Local Project Management](#local-project-management).

## Customizable Commands and Workflows

The AI Toolkit provides **24 commands** that follow workflows in your `docs/development/workflows/` directory and respect conventions in `docs/development/conventions/`.

**Why file-based configuration?** Commands are intentionally minimal - they read your project's guideline files to adapt behavior. Baseline versions come with the toolkit, but keeping them as files in your repo means you can customize them to fit your team's specific workflow and conventions.

**Full command reference**: See `docs/development/misc/commands.md` in your project after running `/toolkit-init`. 

## AI Toolkit Plugin

The AI Toolkit plugin provides a complete development workflow system with **24 commands** organized around a 3-phase development cycle.

> **New to the workflow?** See [Intended Workflow](#intended-workflow) above for a beginner-friendly walkthrough.

### 🚀 Setup & Initialization

- `/toolkit-init` - Initialize project structure with templates and smart conflict resolution
- `/project-brief` - Create and refine project brief through conversational Q&A
- `/spec` - Create and manage feature specs (local files) through natural language
- `/jira-epic` - Create and manage Jira epics (requires optional Jira integration)

### 🌟 Core 3-Phase Workflow

**Phase 1: Architecture**
- `/adr` - Make technical architecture decisions with Quick Mode (5-10 min) or Deep Mode (20+ min)

**Phase 2: Planning**
- `/plan` - Break down tasks into test-first implementation phases with deep analysis and research
  - Uses deep thinking, Context7 library docs, and web research for best practices
  - Takes 3-5 minutes for thorough planning with mandatory context review

**Phase 3: Execution**
- `/implement` - Execute specific phases with test-first enforcement and agent coordination
  - Supports `--next` flag to auto-detect and execute the next uncompleted phase

### 🔍 Quality & Validation

- `/quality` - Multi-dimensional quality assessment using specialized agents
- `/troubleshoot` - Systematic debugging with research-first approach (replaces ad-hoc test fixing)
- `/sanity-check` - Mid-work validation with deep reflection to catch drift early

### 🛠️ Development Support

- `/branch` - Unified branch operations (create, merge, delete, switch, status) with git-workflow enforcement
- `/commit` - Branch-aware git commits with automatic issue references and quality checks
- `/worklog` - Add timestamped work log entries for human-AI collaboration
- `/sync-progress` - Analyze git changes, update plan to reflect progress, and document in WORKLOG
- `/refresh` - Silently refresh AI context by reading project configuration and guidelines

### 🔗 Jira Integration

- `/jira-import` - Import Jira issues for local work with automatic PLAN.md creation
- `/jira-promote` - Promote local exploration issues to Jira for team visibility
- `/jira-comment` - Add AI-suggested comments to Jira issues based on work context

### 📚 Documentation & Project Management

- `/docs` - Unified documentation management (generate, validate, sync, update, health checks)
- `/project-status` - Intelligent project status dashboard with context analysis
- `/ui-design` - Create and iterate on HTML UI mockups with parallel design exploration

### 🏷️ Versioning & Releases

- `/changelog` - Check and update CHANGELOG.md with undocumented changes
- `/release` - Release new version following semantic versioning guidelines

## Key Features

### 21 Specialized Agents

Domain experts that auto-activate based on task context:

- **Strategy & Planning**: brief-strategist, code-architect, project-manager, context-analyzer
- **Cloud Platforms**: aws-expert, azure-expert, gcp-expert
- **Design**: ui-ux-designer, api-designer
- **Implementation**: frontend-specialist, backend-specialist, database-specialist
- **Quality**: test-engineer, code-reviewer, security-auditor, performance-optimizer
- **Operations**: devops-engineer, technical-writer
- **Maintenance**: refactoring-specialist, migration-specialist
- **AI Expertise**: ai-llm-expert

Each agent has specialized tools, domain knowledge, and triggers for automatic invocation based on task requirements.

### Local Project Management

The AI Toolkit provides a complete file-based project management system for teams working entirely locally (no external PM tool required).

> **How does this fit in?** This section shows the file-based implementation of the [Intended Workflow](#intended-workflow) - where specs, tasks, and plans are stored as local files in your repository.

_Note: This approach works best for individuals and small teams. For larger teams, consider Jira or some other dedicated project management tool. More integrations planned._

#### Quick Start

```bash
# 1. Create a feature spec
/spec
→ Creates pm/specs/SPEC-001-user-auth.md

# 2. Create tasks (during /spec or separately)
→ Creates pm/issues/TASK-001-login-form/
→ Creates pm/issues/TASK-002-password-reset/

# 3. Plan implementation
/plan TASK-001
→ Creates TASK-001/PLAN.md with phases

# 4. Execute work
/implement TASK-001 1.1
→ Creates branch: feature/TASK-001
→ Updates TASK-001/WORKLOG.md

# 5. Track progress
/project-status
→ Shows all specs and tasks with completion status
```

#### File Structure

```
pm/
├── specs/
│   └── SPEC-001-user-auth.md       # Feature spec with tasks list
│
└── issues/
    ├── TASK-001-login-form/
    │   ├── TASK.md                  # Requirements & acceptance criteria
    │   ├── PLAN.md                  # AI implementation phases
    │   ├── WORKLOG.md               # Implementation history
    │   └── RESEARCH.md              # Technical decisions (optional)
    │
    └── BUG-001-session-timeout/
        ├── BUG.md
        ├── PLAN.md
        └── WORKLOG.md
```

#### Benefits

- **Fully local** - Works offline, no external dependencies
- **Version controlled** - All planning in git with your code
- **AI-optimized** - Files structured for AI context understanding
- **Flexible** - Customize templates in `docs/development/templates/`
- **Portable** - Self-contained, no vendor lock-in

#### Cons

- **No syncing** - No real way to sync task creation and progress across teams outside of just committing the the repo, which could lead to weird merge conflicts
- **May not scale** - Unsure how well this system will hold up when you have hundreds or even thousands of tasks, may need to implement some kind of an archival process

#### Customization

Customize the workflow by editing templates:
- `docs/development/templates/spec.md` - Feature spec structure and prompts
- `docs/development/templates/task.md` - Task requirements template
- `docs/development/templates/bug.md` - Bug report template

See `docs/development/templates/README.md` for customization guide.

#### Jira Integration (Optional)

Bidirectional sync with Atlassian Jira:
- Import Jira issues: `/jira-import PROJ-123`
- Create Jira issues from local work: `/jira-promote TASK-001`
- Add AI-suggested comments: `/jira-comment PROJ-123`
- Create Jira epics from local specs: `/jira-epic --spec SPEC-001`

**Setup & Usage**: See `docs/development/misc/jira-integration.md` in your project after running `/toolkit-init`

**Status**: Minimal integration. Tested with one Jira project. No automated workflow transitions.

By keeping Jira, or something else, as the source of truth for issues you can remediate some of the syncing and scaling issues mentioned above.

#### Future Integrations

Planned support for Linear, Notion, GitHub Projects, and Azure DevOps.

## Local Development

For plugin development and testing:

```bash
# Add this directory as a local marketplace
cd /path/to/ai-toolkit
/plugin marketplace add ./

# Install the plugin locally
/plugin install ai-toolkit

# Make changes and test immediately
# Updates take effect on plugin reload
```

## Documentation

### Plugin Documentation

See `plugins/ai-toolkit/README.md` for complete plugin documentation including:

- Detailed command reference
- Agent system guide
- Integration patterns
- Troubleshooting

### Starter Template Documentation

The starter template includes comprehensive documentation:

- **docs/project/** - Project-specific documentation (ADRs, design assets)
- **docs/development/** - Development guidelines (customizable templates)
- **pm/** - Project management (feature specs, tasks, bugs with templates)

## Repository Structure

```
ai-toolkit/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace configuration
└── plugins/
    └── ai-toolkit/                # AI Toolkit plugin
        ├── .claude-plugin/
        │   └── plugin.json
        ├── commands/              # 25 slash commands
        ├── agents/                # 21 specialized agents
        ├── templates/             # Bundled project templates
        │   └── starter/           # 49 template files
        ├── docs/                  # Plugin documentation (minimal)
        └── README.md
```

## Requirements

- Claude Code CLI
- Git (for version control commands)
- Bash (for script execution)

Optional but recommended:

- Node.js (for JavaScript/TypeScript projects)
- Python 3.7+ (for Python projects)

## Contributing

Contributions welcome! Please see `CONTRIBUTING.md` for guidelines.

## License

MIT License - see `LICENSE` for details.

## Support

- **Documentation**: Full docs in `plugins/ai-toolkit/README.md` and `starter-template/docs/`
- **Issues**: Report bugs and request features via GitHub Issues
- **Discussions**: Share feedback and ask questions in GitHub Discussions

## Inspiration and Alternatives

Other projects that are also roughly trying to solve the same thing and I find interesting

- [[Taskmaster AI]](https://github.com/eyaltoledano/claude-task-master)
- [[GItHub Spec Kit]](https://github.com/github/spec-kit)
- [[Tessl]](https://tessl.io)