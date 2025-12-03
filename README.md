# Claude SDD

A lightweight, fully customizable framework to enable Spec Driven Development with Claude Code.

## IMPORTANT

This is very much an alpha/experiment at the moment. Look at the commit history to see that for yourself. Right now it's a lot of throwing lots of things at the wall, seeing what works, seeing what doesn't, and frequently changing things as I use it in more "real world" scenarios.

## What's Included

- **AI Toolkit Plugin** - Complete workflow system with 27 commands, 21 specialized agents, and intelligent automation
- **Starter Template** - 51 essential files for clean project initialization via `/toolkit-init`
- **Development Guidelines** - 33 customizable files organized in 4 directories (conventions, workflows, templates, misc)

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

## 21 Specialized Agents

Domain experts that auto-activate based on task context:

- **Strategy**: brief-strategist, code-architect, project-manager, research-specialist
- **Cloud**: aws-expert, azure-expert, gcp-expert
- **Design**: ui-ux-designer, api-designer
- **Implementation**: frontend-specialist, backend-specialist, database-specialist
- **Quality**: test-engineer, code-reviewer, security-auditor, performance-optimizer
- **Operations**: devops-engineer, technical-writer, refactoring-specialist, migration-specialist
- **AI**: ai-llm-expert

## Local Project Management

File-based project management - no external tools required.
_Note: This approach works best for individuals and small teams. For larger teams, consider Jira or some other dedicated project management tool. More integrations planned._

```
pm/
├── specs/
│   └── SPEC-001-user-auth.md       # Feature spec with tasks list
│
└── issues/
    ├── 001-login-form/
    │   ├── TASK.md                  # Requirements & acceptance criteria
    │   ├── PLAN.md                  # AI implementation phases
    │   └── WORKLOG.md               # Implementation history
    │
    └── 002-session-timeout/
        ├── BUG.md
        ├── PLAN.md
        └── WORKLOG.md
```

**Benefits**: Fully local, version controlled, AI-optimized file structure, customizable templates, no vendor lock-in.

**Cons**: No real-time team sync (commits only), may not scale to thousands of tasks without archiving.

**Jira Integration** (optional): Import issues (`/jira-import`), promote local work to Jira (`/jira-promote`), add AI comments (`/jira-comment`). See `docs/development/misc/jira-integration.md` after `/toolkit-init`.

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
