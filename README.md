# AI Workflow Marketplace

A comprehensive AI-assisted development workflow system for Claude Code, providing intelligent task orchestration, specialized agent coordination, and file-based state management.

## IMPORTANT
This is very much an alpha/experiment at this point. Look at the commit history to see that for yourself. Right now it's a lot of throwing lots of things at the wall, seeing what works, seeing what doesn't, and massively changing things as I go.

## What's New in v0.20.0

**Latest additions:**
- 🔄 **Phase commit tracking** - Atomic rollback points for each completed phase with visual commit map
- 📋 **Documentation sync quality gate** - Validates project docs before merge to develop
- 🧹 **Guidelines cleanup** - Reduced verbosity by ~520 lines while preserving essential information
- 📝 **Living documents workflow** - TASK/PLAN/WORKLOG evolve based on implementation learnings

**Recent additions (v0.17.0-0.19.0):**
- ✨ `/plan` enhancement - Deep thinking + library research + best practices (3-5 min thorough planning)
- 🆕 `/sanity-check` - Mid-work validation with deep reflection to catch drift early
- 🆕 `/refresh` - Silent AI context refresh (CLAUDE.md + guidelines + recent commits)
- 🆕 `/comment-issue` - AI-suggested comments for Jira issues based on work context
- ✨ `/implement --next` - Auto-detect and execute next uncompleted phase
- 🔧 WORKLOG format improvements with `[AUTHOR:]` and `[NEXT:]` labels for clarity

See [CHANGELOG.md](CHANGELOG.md) for complete release history.

## What's Included

This marketplace contains:

- **AI Toolkit Plugin** - Complete workflow system with 23 commands, 18 specialized agents, and intelligent automation
- **Starter Template** - 37 essential files for clean project initialization via `/toolkit-init`
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

The AI Toolkit plugin provides a complete development workflow system with **23 commands** organized around a 3-phase development cycle:

### 🚀 Setup & Initialization

- `/toolkit-init` - Initialize project structure with templates and smart conflict resolution
- `/project-brief` - Create and refine project brief through conversational Q&A
- `/epic` - Create and manage epics (local or Jira) through natural language

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

### 20 Specialized Agents

Domain experts that auto-activate based on task context:

- **Strategy & Planning**: brief-strategist, code-architect, project-manager, context-analyzer
- **Design**: ui-ux-designer, api-designer
- **Implementation**: frontend-specialist, backend-specialist, database-specialist
- **Quality**: test-engineer, code-reviewer, security-auditor, performance-optimizer
- **Operations**: devops-engineer, technical-writer
- **Maintenance**: refactoring-specialist, migration-specialist
- **Analytics**: data-analyst
- **AI Expertise**: ai-llm-expert

Each agent has specialized tools, domain knowledge, and triggers for automatic invocation based on task requirements.

### Local Project Management

The AI Toolkit provides a complete file-based project management system for teams working entirely locally (no external PM tool required).

_Note that this probably doesn't scale well across larger teams, and is not intended to be a replacement for Jira/Linear/etc._

#### Quick Start

```bash
# 1. Create an epic
/epic
→ Creates pm/epics/EPIC-001-user-auth.md

# 2. Create tasks (during /epic or separately)
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
→ Shows all epics and tasks with completion status
```

#### File Structure

```
pm/
├── epics/
│   └── EPIC-001-user-auth.md       # Epic with tasks list
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
- `pm/templates/epic.md` - Epic structure and prompts
- `pm/templates/task.md` - Task requirements template
- `pm/templates/bug.md` - Bug report template

See `pm/templates/README.md` for customization guide.

### Working With Jira

The AI Toolkit integrates with Atlassian Jira for teams using Jira as their project management system.

This integration is currently minimal. It has only been tested with one Jira project, and currently doesn't involve things like actually transitioning Jira issues through a workflow or anything. More functionality coming soon.

#### Requirements

- **Atlassian Remote MCP Server** configured in Claude Code
- Jira Cloud account with appropriate permissions
- Project Key (e.g., "PROJ") for your Jira project

#### Setup

1. **Configure Atlassian Remote MCP Server** (see [Atlassian's guide](https://www.atlassian.com/blog/announcements/remote-mcp-server))
2. **Enable Jira in CLAUDE.md:**
   ```yaml
   ## Jira Integration
   - **Enabled**: true
   - **MCP Server**: Atlassian Remote MCP
   - **Project Key**: PROJ
   ```
3. **Run your first command:**
   ```bash
   /epic  # Creates epic in Jira
   ```

#### How It Works

**Epics → Jira Only**
- All epics live in Jira (PROJ-100, PROJ-200)
- Use `/epic` to create epics conversationally
- AI discovers required fields automatically

**Issues → Jira or Local**
- **Jira issues** (PROJ-123): Import with `/import-issue PROJ-123`
- **Local exploration** (TASK-001): Create with `/plan TASK-001`
- **Promotion**: Use `/promote TASK-001` to create Jira issue from local spike

**What Syncs:**
- ✅ **Read from Jira**: Description, acceptance criteria, status (display only)
- ✅ **Create in Jira**: Epics, stories, tasks via AI conversation
- ✅ **Add comments**: AI-suggested comments based on local work context
- ❌ **Not synced**: Status updates, field changes, automatic comment sync

**Local Storage:**
- `PLAN.md` - AI implementation phases (local only)
- `WORKLOG.md` - Implementation history (local only)
- `RESEARCH.md` - Technical decisions (local only)
- No TASK.md for Jira issues (fetched on-demand)

#### Common Workflows

**Working on Jira Issue:**
```bash
/import-issue PROJ-123         # Pull from Jira
/plan PROJ-123                 # Create implementation plan
/implement PROJ-123 1.1        # Execute phase
/commit                        # Commit changes
/comment-issue PROJ-123        # AI suggests progress comment for Jira
# Update Jira status manually in UI
```

**Exploration Then Promotion:**
```bash
/plan TASK-001                 # Quick local spike
/implement TASK-001 1.1        # Prototype
/promote TASK-001              # Create PROJ-125 in Jira
/plan PROJ-125                 # Continue with Jira issue
```

**Creating Epics + Issues:**
```bash
/epic                          # AI guides epic creation
→ Creates PROJ-100 in Jira
→ Optionally creates initial issues
/plan PROJ-123                 # Start implementation
```

#### Limitations

- **Status updates**: Not automated. Update Jira status manually in UI.
- **Comment sync**: Use `/comment-issue` to manually add comments. Local WORKLOG.md is not automatically synced.
- **Custom workflows**: AI doesn't trigger Jira workflow transitions.
- **Offline work**: Can work on existing issues with cached data, but can't create new Jira issues or add comments offline.
- **One project**: Currently supports one Jira project key per repository.

#### Troubleshooting

**"Atlassian MCP unavailable"**
- Ensure Atlassian Remote MCP Server is configured in Claude Code MCP settings
- Check network/VPN connection
- Verify Jira permissions

**"Field 'X' is required"**
- Run `/refresh-schema` to update field cache
- Jira admin may have added new required fields

**"Issue not found"**
- Verify issue exists in Jira
- Check project key is correct in CLAUDE.md
- Ensure you have permission to view issue

### File-Based State Management

Seamless session continuity through structured files:

- **EPIC.md** - Epic-level context and progress
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
- **pm/** - Project management (epics, tasks, bugs with templates)

## Repository Structure

```
ai-toolkit/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace configuration
└── plugins/
    └── ai-toolkit/                # AI Toolkit plugin
        ├── .claude-plugin/
        │   └── plugin.json
        ├── commands/              # 23 slash commands
        ├── agents/                # 18 specialized agents
        ├── templates/             # Bundled project templates
        │   └── starter/           # 33 template files
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

## Version

Current version: 0.17.0

See `CHANGELOG.md` for release history and `STATUS.md` for current development status.
