# Claude SDD

A lightweight, fully customizable framework to enable Spec Driven Development with Claude Code.

## IMPORTANT

This is very much an alpha/experiment at the moment. Look at the commit history to see that for yourself. Right now it's a lot of throwing lots of things at the wall, seeing what works, seeing what doesn't, and frequently changing things as I use it in more "real world" scenarios.

## What's Included

- **AI Toolkit Plugin** - Complete workflow system with 26 commands, 21 specialized agents, and intelligent automation
- **Starter Template** - 50 essential files for clean project initialization via `/toolkit-init`
- **Development Guidelines** - 34 customizable files organized in 4 directories (conventions, workflows, templates, misc)

## Quick Start

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
- Structured template (50 files: docs/, pm/, development guidelines, templates)
- GETTING-STARTED.md guide
- Documentation framework (AI creates content as you work)
- Interactive setup with smart conflict resolution

Run `/toolkit-init --dry-run` after plugin updates to preview template changes. See `GETTING-STARTED.md` in your initialized project for detailed update workflows.

## Intended Workflow

1. **`/project-brief`** - Create your Project Brief through an interactive session. This is the high-level, relatively non-technical overview of your project: the problem you're solving, your target audience, core features. Think "One Pager" or "Elevator Pitch" - the guiding direction for everything else.

2. **`/adr`** - Create Architecture Decision Records for technical decisions ("What database should I use?", "Deploy to Vercel or Netlify?"). ADRs are intended to be static once approved - if something needs changing, create a new ADR to supersede it.

3. **`/spec`** - Create a Spec Document (the heart of the workflow). A Spec is a concrete plan for a body of related work - similar in scope to an Epic in Agile or a PRD. Written for easy parsing by Claude Code with clear requirements, acceptance criteria, and definition of done. Unlike ADRs, Specs are living documents that adapt as you discover hiccups and make adjustments during implementation.

4. **`/plan ###`** - Create a thorough implementation plan with all steps needed to complete the task. In the default configuration, this breaks work into discrete Phases (each a logical commit with strict TDD workflow), but you can customize to your specifications. Works for Tasks, Bugs and Spikes.

5. **`/implement ### PHASE`** - Start implementing. Execute a single phase or let Claude complete the entire task end-to-end. Quality gates and workflows make autonomous execution both possible and safe, but you're always in control.

## Core Commands

| Command | Purpose |
|---------|---------|
| `/project-brief` | Create/update project vision through interactive Q&A |
| `/spec` | Create feature specs with acceptance criteria |
| `/adr` | Make architecture decisions (Quick Mode or Deep Mode) |
| `/plan ###` | Break down tasks into test-first implementation phases |
| `/implement ### PHASE` | Execute phases with specialized agents and quality gates |
| `/quality` | Multi-dimensional quality assessment before merge |

**Full reference**: See `docs/development/misc/commands.md` after running `/toolkit-init`.

**Why file-based configuration?** Commands are intentionally minimal - they read your project's guideline files to adapt behavior. Baseline versions come with the toolkit, but keeping them as files in your repo means you can customize them to fit your team's specific workflow and conventions.

**Full command reference**: See `docs/development/misc/commands.md` in your project after running `/toolkit-init`.

## AI Toolkit Plugin

The AI Toolkit plugin provides a complete development workflow system with **26 commands** organized around a 3-phase development cycle.

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
- `/issue` - Create tasks, bugs, or spikes with AI-assisted type detection
  - Detects type from description: "fix broken login" → BUG, "should we use GraphQL?" → SPIKE
  - Creates `pm/issues/###-name/` with appropriate file (TASK.md, BUG.md, or SPIKE.md)
- `/plan` - Break down tasks into test-first implementation phases with deep analysis and research
  - Uses deep thinking, Context7 library docs, and web research for best practices
  - Takes 3-5 minutes for thorough planning with mandatory context review

**Phase 3: Execution**
- `/implement` - Execute specific phases with test-first enforcement and agent coordination
  - Supports `--next` flag to auto-detect and execute the next uncompleted phase
- `/advise` - Get implementation guidance without automated code generation (collaborative mode)
  - User implements manually following structured advice from AI agents
- `/complete` - Unified issue completion with workflow-specific validation
  - TASK: phases complete, tests passing, review ≥90, acceptance criteria met
  - BUG: reproduction test exists, fix works, no regressions
  - SPIKE: pulls WORKLOGs, generates SPIKE-SUMMARY.md

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

- **Strategy**: brief-strategist, code-architect, project-manager, context-analyzer
- **Cloud**: aws-expert, azure-expert, gcp-expert
- **Design**: ui-ux-designer, api-designer
- **Implementation**: frontend-specialist, backend-specialist, database-specialist
- **Quality**: test-engineer, code-reviewer, security-auditor, performance-optimizer
- **Operations**: devops-engineer, technical-writer, refactoring-specialist, migration-specialist
- **AI**: ai-llm-expert

## Local Project Management

File-based project management - no external tools required.
_Note: This approach works best for individuals and small teams. For larger teams, consider Jira or some other dedicated project management tool. More integrations planned._

#### Quick Start

```bash
# 1. Create a feature spec
/spec
→ Creates pm/specs/SPEC-001-user-auth.md

# 2. Create tasks (during /spec or separately)
→ Creates pm/issues/001-login-form/
→ Creates pm/issues/002-password-reset/

# 3. Plan implementation
/plan 001
→ Creates 001/PLAN.md with phases

# 4. Execute work
/implement 001 1.1
→ Creates branch: feature/001
→ Updates 001/WORKLOG.md

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
    ├── 001-login-form/
    │   ├── TASK.md                  # Requirements & acceptance criteria (type from file)
    │   ├── PLAN.md                  # AI implementation phases
    │   └── WORKLOG.md               # Implementation history
    │
    └── 002-session-timeout/
        ├── BUG.md                   # Bug report (type from file)
        ├── PLAN.md
        └── WORKLOG.md
```

**Benefits**: Fully local, version controlled, AI-optimized file structure, customizable templates, no vendor lock-in.

**Cons**: No real-time team sync (commits only), may not scale to thousands of tasks without archiving.

#### Customization

Customize the workflow by editing templates:
- `docs/development/templates/spec-template.md` - Feature spec structure and prompts
- `docs/development/templates/task-template.md` - Task requirements template
- `docs/development/templates/bug-template.md` - Bug report template

See `docs/development/templates/README.md` for customization guide.

#### Jira Integration (Optional)

Bidirectional sync with Atlassian Jira:
- Import Jira issues: `/jira-import PROJ-123`
- Create Jira issues from local work: `/jira-promote 001`
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
        ├── commands/              # 26 slash commands (see /help for list)
        ├── agents/                # 21 specialized agents
        ├── templates/             # Bundled project templates
        │   └── starter/           # 50 template files
        ├── docs/                  # Plugin documentation (minimal)
        └── README.md
```

## Requirements

- Claude Code CLI
- Git (for version control commands)
- Bash (for script execution)

## Contributing

Contributions welcome! Please see `CONTRIBUTING.md` for guidelines.

## License

MIT License - see `LICENSE` for details.

## Support

- **Issues**: Report bugs and request features via GitHub Issues
- **Discussions**: Share feedback and ask questions in GitHub Discussions

## Inspiration and Alternatives

Other projects that are also roughly trying to solve the same thing and I find interesting

- [Taskmaster AI](https://github.com/eyaltoledano/claude-task-master)
- [GitHub Spec Kit](https://github.com/github/spec-kit)
- [Tessl](https://tessl.io)
- [ClaudeKit](https://claudekit.cc)
