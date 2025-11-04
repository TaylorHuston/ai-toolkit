# AI Workflow Marketplace

A comprehensive AI-assisted development workflow system for Claude Code, providing intelligent task orchestration, specialized agent coordination, and file-based state management.

## IMPORTANT
This is very much an alpha/experiment at this point. Look at the commit history to see that for yourself. Right now it's a lot of throwing lots of things at the wall, seeing what works, seeing what doesn't, and massively changing things as I go.

## What's Included

This marketplace contains:

- **AI Toolkit Plugin** - Complete workflow system with 22 commands, 20 specialized agents, and intelligent automation
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

The AI Toolkit plugin provides a complete development workflow system:

### Setup & Planning Commands

- `/toolkit-init` - Initialize project structure with templates (can also be used to bring in changes and improvements when the plugin is updated intelligently)
- `/project-brief` - Create and refine project brief through conversation
- `/epic` - Create and manage epics through natural language
- `/adr` - Make technical architecture decisions (ADRs)
- `/ui-design` - Create HTML UI mockups with parallel design exploration
- `/plan` - Break down tasks into implementation phases

### Implementation Commands

- `/implement` - Execute specific task phases with test-first approach

### Quality & Testing Commands

- `/quality` - Multi-dimensional quality assessment
- `/security-audit` - OWASP-compliant security assessment
- `/test-fix` - Automated test failure resolution

### Development Workflow Commands

- `/commit` - Quality-checked git commits
- `/branch` - Unified branch operations (create, merge, delete, switch, status)
- `/comment` - Add timestamped work log entries

### Documentation & Status Commands

- `/docs` - Unified documentation management (generate, validate, sync, update)
- `/project-status` - Intelligent project status dashboard

### Versioning & Releases Commands

- `/changelog` - Check and update CHANGELOG.md with undocumented changes
- `/release` - Release new version following semantic versioning guidelines

## Key Features

### 19 Specialized Agents

Domain experts that auto-activate based on task context:

- **Strategy & Design**: brief-strategist, code-architect, ui-ux-designer
- **Implementation**: frontend-specialist, backend-specialist, database-specialist
- **Quality**: test-engineer, code-reviewer, security-auditor, performance-optimizer
- **Operations**: devops-engineer, technical-writer
- **Analysis**: context-analyzer, project-manager, api-designer
- **Maintenance**: refactoring-specialist, migration-specialist, data-analyst
- **AI Expertise**: ai-llm-expert

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
- ❌ **Not synced**: Status updates, comments, field changes

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
- **Comments**: Local WORKLOG.md not synced to Jira comments.
- **Custom workflows**: AI doesn't trigger Jira workflow transitions.
- **Offline work**: Can work on existing issues with cached data, but can't create new Jira issues offline.
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
        ├── commands/              # 22 slash commands
        ├── agents/                # 19 specialized agents
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

Current version: 0.16.0

See `CHANGELOG.md` for release history and `STATUS.md` for current development status.
