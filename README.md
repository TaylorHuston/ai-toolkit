# AI Workflow Marketplace

A comprehensive AI-assisted development workflow system for Claude Code, providing intelligent task orchestration, specialized agent coordination, and file-based state management.

## IMPORTANT
This is very much an alpha/experiment at this point. Look at the commit history to see that for yourself. Right now it's a lot of throwing lots of things at the wall, seeing what works, seeing what doesn't, and massively changing things as I go.

## What's New in v0.26.0

**Latest additions:**
- 🔄 **`/sync-progress` command** - Automatically sync project state after manual changes
  - Analyzes git diff to understand uncommitted/staged changes
  - Updates PLAN.md to reflect completed phases or new direction
  - Documents in WORKLOG with inferred intent and context
  - Suggests next steps based on remaining plan
  - Perfect for when you make manual changes offline and want AI to catch up

**Recent additions (v0.25.0):**
- ☁️ **Cloud platform expert agents** - Three new specialized agents for cloud architecture
  - **aws-expert** - AWS Solutions Architect (Well-Architected Framework, cost optimization)
  - **azure-expert** - Azure Solutions Architect (Microsoft ecosystem, hybrid cloud)
  - **gcp-expert** - Google Cloud Solutions Architect (Kubernetes, BigQuery, Vertex AI)
  - Multi-cloud comparison and platform-specific implementation guidance
  - All use claude-opus-4-1 for critical architectural decisions

**Recent additions (v0.24.0):**
- 🔧 **Agent standardization** - All 21 agents aligned with agent-template.md format
  - Universal Rules section added (CLAUDE.md + WORKLOG protocols)
  - Condensed verbose agents to meet 350-line limit
  - Enhanced Context7 integration references

**Recent additions (v0.23.0):**
- 🔄 **Epic → Feature Spec terminology pivot** - Aligned with spec-driven development trends (BREAKING CHANGE)
- ✨ **`/spec` command** - Create and manage local feature specifications with bidirectional Jira integration
- 🔧 **`/epic` command refactored** - Now Jira-only (requires integration enabled)

See [CHANGELOG.md](CHANGELOG.md) for complete release history.

## What's Included

This marketplace contains:

- **AI Toolkit Plugin** - Complete workflow system with 26 commands, 21 specialized agents, and intelligent automation
- **Starter Template** - 39 essential files for clean project initialization via `/toolkit-init`
- **Guideline Templates** - 8 customizable guideline templates (project configuration files, not plugin docs)

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
- Structured template (37 files: docs/, pm/, guidelines, templates)
- GETTING-STARTED.md guide
- Documentation framework (AI creates content as you work)
- Interactive setup with smart conflict resolution

## AI Toolkit Plugin

The AI Toolkit plugin provides a complete development workflow system with **26 commands** organized around a 3-phase development cycle:

### 🚀 Setup & Initialization

- `/toolkit-init` - Initialize project structure with templates and smart conflict resolution
- `/project-brief` - Create and refine project brief through conversational Q&A
- `/spec` - Create and manage feature specs (local files) through natural language
- `/epic` - Create and manage Jira epics (requires Jira integration)

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
- `/security-audit` - OWASP-compliant security assessment with vulnerability remediation
- `/troubleshoot` - Systematic debugging with research-first approach (replaces ad-hoc test fixing)
- `/sanity-check` - Mid-work validation with deep reflection to catch drift early

### 🛠️ Development Support

- `/branch` - Unified branch operations (create, merge, delete, switch, status) with git-workflow enforcement
- `/commit` - Branch-aware git commits with automatic issue references and quality checks
- `/comment` - Add timestamped work log entries for human-AI collaboration
- `/sync-progress` - Analyze git changes, update plan to reflect progress, and document in WORKLOG
- `/refresh` - Silently refresh AI context by reading project configuration and guidelines

### 🔗 Jira Integration

- `/import-issue` - Import Jira issues for local work with automatic PLAN.md creation
- `/promote` - Promote local exploration issues to Jira for team visibility
- `/comment-issue` - Add AI-suggested comments to Jira issues based on work context
- `/refresh-schema` - Refresh Jira field schema cache when requirements change

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

_Note that this probably doesn't scale well across larger teams, and is not intended to be a replacement for Jira/Linear/etc._

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

- ✅ **Fully local** - Works offline, no external dependencies
- ✅ **Version controlled** - All planning in git with your code
- ✅ **AI-optimized** - Files structured for AI context understanding
- ✅ **Flexible** - Customize templates in `pm/templates/`
- ✅ **Portable** - Self-contained, no vendor lock-in

#### Customization

Customize the workflow by editing templates:
- `pm/templates/spec.md` - Feature spec structure and prompts
- `pm/templates/task.md` - Task requirements template
- `pm/templates/bug.md` - Bug report template

See `pm/templates/README.md` for customization guide.

### 🔗 Project Management Integrations

The AI Toolkit supports both **local file-based project management** and **external PM tool integrations**.

#### Local PM (Built-in)

File-based specs and tasks in `pm/` directory:
- ✅ Works offline, fully version controlled
- ✅ Perfect for individuals and small teams
- ✅ No external dependencies
- See [Local Project Management](#local-project-management) section above

#### Jira Integration (Optional)

Bidirectional sync with Atlassian Jira:
- Import Jira issues: `/import-issue PROJ-123`
- Create Jira issues from local work: `/promote TASK-001`
- Add AI-suggested comments: `/comment-issue PROJ-123`
- Create Jira epics from local specs: `/epic --spec SPEC-001`

**Setup & Usage**: See [docs/jira-integration.md](plugins/ai-toolkit/docs/jira-integration.md)

**Status**: Minimal integration. Tested with one Jira project. No automated workflow transitions.

#### Future Integrations

Planned support for:
- Linear
- Notion
- GitHub Projects
- Azure DevOps

### File-Based State Management

Seamless session continuity through structured files:

- **SPEC.md** - Feature spec context and progress
- **TASK-###-*/TASK.md** - Task-specific implementation details
- **WORKLOG.md** - Narrative work history with lessons learned
- **RESEARCH.md** - Investigation findings and technical decisions

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
        ├── commands/              # 26 slash commands
        ├── agents/                # 21 specialized agents
        ├── templates/             # Bundled project templates
        │   └── starter/           # 39 template files
        ├── docs/                  # Plugin documentation
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