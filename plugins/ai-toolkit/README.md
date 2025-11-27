# AI Toolkit Plugin

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)

Comprehensive AI-assisted development workflow system for Claude Code with 26 commands, 21 specialized agents, and intelligent state management.

## Quick Start

```bash
# 1. Install plugin
/plugin install ai-toolkit

# 2. Initialize your project
cd my-project
/toolkit-init

# 3. Start developing
/project-brief
/spec SPEC-001
/plan 001
/implement 001 1.1
```

## What You Get

- **26 Workflow Commands** - Complete `/project-brief` → `/spec` → `/issue` → `/plan` → `/implement` → `/complete` cycle + utilities
- **21 Specialized Agents** - Domain experts (frontend, backend, security, testing, etc.)
- **3 Bundled MCP Servers** - Auto-configured tools (context7, sequential-thinking, playwright)
- **Starter Template** - 50 files for organized project initialization
- **File-Based State** - Session continuity via SPEC.md, TASK.md, WORKLOG.md
- **Technology Agnostic** - Works with any tech stack

## Bundled MCP Servers

This plugin automatically configures essential MCP servers:

- **context7** - Library documentation and code examples
- **sequential-thinking** - Complex problem decomposition
- **playwright** - Browser automation for testing

No manual MCP configuration required! These tools are ready immediately after plugin installation.

**Optional MCP Servers**: For larger codebases (20+ files), see `/docs/development/misc/optional-mcp-servers.md` in your project after `/toolkit-init` for additional integrations like Serena for semantic code analysis.

## Model Selection Philosophy

The AI Toolkit uses different Claude models strategically based on task requirements:

### Sonnet 4.5 - Primary Workhorse (14 agents + 21 commands)

**Performance**: Best coding model in the world (77.2% SWE-bench), strongest for agents (30+ hours autonomous), best computer use (61.4% OSWorld)

**Cost**: $3/$15 per million tokens (5x cheaper than Opus)

**Used For**:
- **All specialist agents**: frontend, backend, database, devops, api, performance, ui-ux, data, migration, refactoring
- **Code-focused agents**: code-architect, test-engineer, code-reviewer, technical-writer, context-analyzer
- **Execution commands**: /implement, /complete, /issue, /plan, /quality, /branch, /commit, /docs, /project-status, /toolkit-init, /worklog, /changelog, /release, /troubleshoot, /sanity-check, /refresh, /jira-comment, /jira-promote, /jira-import, /ui-design, /sync-progress

**Why**: Sonnet 4.5 excels at coding, code generation, documentation, and autonomous operation. Its superior performance at lower cost makes it ideal for hands-on development work.

### Opus 4.5 - Strategic Reasoning (7 agents + 4 commands)

**Performance**: Extended thinking tokens, exceptional multi-step reasoning, superior strategic analysis

**Cost**: $15/$75 per million tokens (premium pricing)

**Used For**:
- **Strategic agents**: project-manager (orchestration), security-auditor (safety-critical), brief-strategist (product strategy), ai-llm-expert (meta-reasoning), aws-expert, azure-expert, gcp-expert (cloud architecture)
- **Planning commands**: /project-brief, /spec, /jira-epic, /adr

**Why**: Opus 4.5's extended reasoning and superior strategic capabilities make it ideal for high-stakes decisions, strategic planning, security analysis, cloud architecture, and complex orchestration.

### Selection Criteria

**Choose Sonnet 4.5 when:**
- Writing or analyzing code
- Generating documentation
- Building features autonomously
- Multi-file refactoring
- Test creation and execution
- Context analysis and investigation

**Choose Opus 4.5 when:**
- Strategic planning (epics, briefs, ADRs)
- Security-critical decisions
- Complex orchestration across multiple agents
- High-stakes architecture decisions
- Cloud architecture and infrastructure design
- Extended multi-step reasoning needed

### Cost Optimization

Using Sonnet 4.5 for execution work (coding, testing, documentation) while reserving Opus 4.5 for strategic decisions provides optimal cost/performance:

- **~85% of work**: Sonnet 4.5 at $3/$15 (coding, implementation, documentation, analysis)
- **~15% of work**: Opus 4.5 at $15/$75 (planning, strategy, security, cloud architecture)
- **Result**: ~4x cost savings while maintaining quality where it matters

**Users can override model selection** in command/agent frontmatter if their use case requires different allocation.

## Installation

```bash
/plugin install ai-toolkit
```

Commands and agents available immediately. Initialize your project structure with:

```bash
cd your-project
/toolkit-init
```

This creates:

```
my-project/
├── CLAUDE.md             # Customized project context
├── README.md             # Project template
├── GETTING-STARTED.md    # AI Toolkit usage guide
├── .gitignore            # Standard ignores
├── docs/
│   ├── README.md         # Documentation guide
│   ├── project-brief.md  # Your project vision
│   ├── project/          # Project-specific docs (created by AI)
│   └── development/      # Links to plugin guidelines
└── pm/                   # Project management (specs, issues)
```

**AI creates structure as you work** - no empty placeholders or stale examples!

## Core Workflow

```bash
/project-brief                     # Define project vision (interactive)
/spec SPEC-001                     # Create feature spec with acceptance criteria
/plan 001                          # Add implementation plan to task
/implement 001 1.1                 # Execute specific phase with agents
```

## All Commands (26 Total)

**Setup & Strategy**: `/toolkit-init`, `/project-brief`
**Spec & Epic Management**: `/spec`, `/jira-epic`, `/jira-import`
**Workflow**: `/adr`, `/issue`, `/plan`, `/implement`, `/advise`, `/complete`
**Quality**: `/quality`, `/troubleshoot`, `/sanity-check`
**Development**: `/branch`, `/commit`, `/worklog`, `/sync-progress`
**Documentation & Status**: `/docs`, `/project-status`, `/changelog`, `/release`
**Utilities**: `/refresh`, `/ui-design`, `/jira-comment`, `/jira-promote`

See `/docs/development/misc/commands.md` in your project after `/toolkit-init` for complete command reference.

## Documentation

After running `/toolkit-init`, find complete documentation in your project:
- `/docs/development/misc/commands.md` - Complete command reference with workflow guidance
- `/docs/development/misc/agents.md` - Agent catalog with system overview
- `/docs/development/misc/optional-mcp-servers.md` - Optional MCP servers for larger projects

## Updates

```bash
/plugin update ai-toolkit
```

All commands/agents update automatically. Your project files unchanged.

## Support

- [Issues](https://github.com/TaylorHuston/ai-toolkit/issues)
- [Discussions](https://github.com/TaylorHuston/ai-toolkit/discussions)

## License

MIT - See LICENSE file
