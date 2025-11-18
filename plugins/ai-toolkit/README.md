# AI Toolkit Plugin

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)

Comprehensive AI-assisted development workflow system for Claude Code with 23 commands, 18 specialized agents, and intelligent state management.

## Quick Start

```bash
# 1. Install plugin
/plugin install ai-toolkit

# 2. Initialize your project
cd my-project
/toolkit-init

# 3. Start developing
/project-brief
/epic
/plan TASK-001
/implement TASK-001 1.1
```

## What You Get

- **23 Workflow Commands** - Complete `/project-brief` → `/epic` → `/plan` → `/implement` cycle + utilities
- **18 Specialized Agents** - Domain experts (frontend, backend, security, testing, etc.)
- **3 Bundled MCP Servers** - Auto-configured tools (context7, sequential-thinking, playwright)
- **Starter Template** - 37 files (9 core + 28 structure) for organized project initialization
- **File-Based State** - Session continuity via EPIC.md, TASK.md, WORKLOG.md, RESEARCH.md
- **Technology Agnostic** - Works with any tech stack

## Bundled MCP Servers

This plugin automatically configures essential MCP servers:

- **context7** - Library documentation and code examples
- **sequential-thinking** - Complex problem decomposition
- **playwright** - Browser automation for testing

No manual MCP configuration required! These tools are ready immediately after plugin installation.

**Optional MCP Servers**: For larger codebases (20+ files), consider adding [Serena](./docs/OPTIONAL-MCP-SERVERS.md) for semantic code analysis.

## Model Selection Philosophy

The AI Toolkit uses different Claude models strategically based on task requirements:

### Sonnet 4.5 - Primary Workhorse (13 agents + 20 commands)

**Performance**: Best coding model in the world (77.2% SWE-bench), strongest for agents (30+ hours autonomous), best computer use (61.4% OSWorld)

**Cost**: $3/$15 per million tokens (5x cheaper than Opus)

**Used For**:
- **All specialist agents**: frontend, backend, database, devops, api, performance, ui-ux, data, migration, refactoring
- **Code-focused agents**: code-architect, test-engineer, code-reviewer, technical-writer
- **Execution commands**: /implement, /plan, /quality, /branch, /commit, /docs, /project-status, /toolkit-init, /worklog, /changelog, /release, /troubleshoot, /sanity-check, /refresh, /comment-issue, /promote, /refresh-schema, /import-issue, /ui-design

**Why**: Sonnet 4.5 excels at coding, code generation, documentation, and autonomous operation. Its superior performance at lower cost makes it ideal for hands-on development work.

### Opus 4.1 - Strategic Reasoning (4 agents + 4 commands)

**Performance**: 64K extended thinking tokens, 98.76% safety refusal rate, exceptional multi-step reasoning

**Cost**: $15/$75 per million tokens (premium pricing)

**Used For**:
- **Strategic agents**: project-manager (orchestration), security-auditor (safety-critical), brief-strategist (product strategy), ai-llm-expert (meta-reasoning)
- **Planning commands**: /project-brief, /epic, /adr, /security-audit

**Why**: Opus 4.1's extended reasoning (64K thinking tokens) and superior safety make it ideal for high-stakes decisions, strategic planning, security analysis, and complex orchestration.

### Haiku - Fast Analysis (1 agent)

**Performance**: Fastest Claude model, optimized for speed

**Used For**:
- **context-analyzer**: Quick investigation, bug diagnosis, evidence gathering

**Why**: When speed matters more than depth, Haiku provides rapid analysis for initial investigation and context gathering.

### Selection Criteria

**Choose Sonnet 4.5 when:**
- Writing or analyzing code
- Generating documentation
- Building features autonomously
- Multi-file refactoring
- Test creation and execution

**Choose Opus 4.1 when:**
- Strategic planning (epics, briefs, ADRs)
- Security-critical decisions
- Complex orchestration across multiple agents
- High-stakes architecture decisions
- Extended multi-step reasoning needed

**Choose Haiku when:**
- Quick context analysis
- Fast investigation
- Initial bug triage

### Cost Optimization

Using Sonnet 4.5 for execution work (coding, testing, documentation) while reserving Opus 4.1 for strategic decisions provides optimal cost/performance:

- **90% of work**: Sonnet 4.5 at $3/$15 (coding, implementation, documentation)
- **10% of work**: Opus 4.1 at $15/$75 (planning, strategy, security)
- **Result**: ~5x cost savings while maintaining quality where it matters

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
└── pm/pm/epics/                # Epic-based work (auto-created)
```

**AI creates structure as you work** - no empty placeholders or stale examples!

## Core Workflow

```bash
/project-brief                     # Define project vision (interactive)
/epic                              # Create epic with optional initial tasks
/epic EPIC-001                     # Refine epic, add more tasks (iterative)
/plan TASK-001                     # Add implementation plan to task
/implement TASK-001 1.1            # Execute specific phase with agents
```

## All Commands (14 Total)

**Setup & Strategy**: `/toolkit-init`, `/project-brief`
**Epic Management**: `/epic`
**Workflow**: `/adr`, `/plan`, `/implement`
**Quality**: `/quality`, `/security-audit`, `/troubleshoot`
**Development**: `/branch`, `/commit`, `/worklog`
**Documentation & Status**: `/docs`, `/project-status`

See `docs/COMMANDS.md` for complete command reference.

## Documentation

**Plugin Documentation:**
- `docs/COMMANDS.md` - Complete command reference with workflow guidance
- `docs/AGENTS.md` - Agent catalog with system overview
- `docs/OPTIONAL-MCP-SERVERS.md` - Optional MCP servers for larger projects

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
