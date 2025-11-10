# Getting Started with AI Toolkit

Welcome! You've initialized your project with the AI Toolkit for Claude Code.

## Your Project Structure

```
your-project/
├── CLAUDE.md               # Project context for AI
├── README.md               # Project overview
├── GETTING-STARTED.md      # This file
├── docs/
│   ├── project-brief.md    # Your vision (start here!)
│   ├── project/            # Architecture, ADRs, design assets
│   └── development/        # Guidelines (8 customizable templates)
└── pm/
    ├── specs/              # Feature planning
    ├── issues/             # Tasks and bugs
    └── templates/          # Issue templates
```

**37 files organized for clarity:**
- **9 core files**: Project essentials (CLAUDE.md, README.md, CHANGELOG.md, etc.)
- **28 structure files**: Guidelines (8), templates (5), documentation (9), placeholders (6)

**Why this approach?** Guidelines and templates provide structure without overwhelming you. The AI Toolkit builds content as you work - no stale examples, just living documentation.

## Quick Start

### 1. Define Your Vision
```bash
/project-brief
```
Interactive conversation to create your project brief - the "what" and "why" of your project.

### 2. Plan Your First Feature
```bash
/spec
```
Create an spec to organize related work. The AI helps you break it down into tasks.

### 3. Make Architecture Decisions
```bash
/adr
```
Explore technical solutions and create ADRs (Architecture Decision Records).

### 4. Plan Implementation
```bash
/plan TASK-001
```
Break tasks into implementation phases with clear steps and testing requirements.

### 5. Build It
```bash
/implement TASK-001 1.1
```
Execute specific phases with specialized AI agents (frontend, backend, test, security, etc.).

## Git Workflow

The AI Toolkit enforces a **three-branch workflow** for production safety:

```
main (production)         # Live environment - ONLY from develop
  ↑
  └─ develop (staging)    # Pre-production - from feature branches
       ↑
       ├─ feature/TASK-001  (your work)
       ├─ feature/TASK-002
       └─ bugfix/BUG-001
```

**CRITICAL RULES:**
- ✅ Work branches → develop ONLY
- ✅ Only develop → main
- ❌ NEVER merge work branches directly to main

### Branch Management

**Work branches are created automatically:**
```bash
/implement TASK-001 1.1
# → Creates feature/TASK-001 if needed
# → Switches to the branch
# → Executes the phase
```

**Merging to staging (with test validation):**
```bash
/branch merge develop
# → Runs all tests
# → BLOCKS if any fail
# → Merges if all pass
# ✅ feature/* → develop (allowed)
```

**Promoting to production (with staging validation):**
```bash
# ❌ WRONG - work branches cannot merge to main:
# /branch merge main  # BLOCKED by /branch command

# ✅ CORRECT - two-step process via develop:
/branch switch develop
/branch merge main
# → Verifies source is develop (BLOCKS work branches)
# → Runs staging health checks
# → Validates deployment
# → Merges if validated
```

**Clean up after merge:**
```bash
/branch delete feature/TASK-001
# → Verifies fully merged
# → Deletes local and remote
```

### Commit Messages

Branch-aware commits automatically include issue references:

```bash
# On feature/TASK-001
/commit "add user authentication"
# → Generates: feat(TASK-001): add user authentication
```

### Workflow Configuration

Your git workflow is defined in `docs/development/guidelines/git-workflow.md`:
- Branch naming patterns
- Merge validation rules
- Commit message format
- Production safety requirements

Commands automatically read and enforce these rules.

## How It Works

### Commands Guide You
Each command is conversational and guides you through its workflow:
- `/project-brief` asks questions to fill in your vision
- `/spec` helps structure features with acceptance criteria
- `/adr` explores options and creates ADRs
- `/plan` breaks work into testable phases
- `/implement` executes with domain-specific agents

### Structure Emerges
As you work, the AI creates documentation automatically:
- **ADRs** from `/adr` sessions
- **Task plans** from `/plan` command
- **Implementation notes** during `/implement`
- **Test plans** integrated throughout

### Guidelines Adapt
Your project includes 8 customizable guideline templates in `docs/development/guidelines/`:
- `development-loop.md` - AI-assisted workflow and quality gates
- `api-guidelines.md` - API patterns and structure
- `testing-standards.md` - Testing approach
- `git-workflow.md` - Branching and commits
- `versioning-and-releases.md` - Semantic versioning, releases, and CHANGELOG maintenance
- `coding-standards.md` - Code style
- `security-guidelines.md` - Security practices
- `architectural-principles.md` - Design philosophy

Start with TBD placeholders, fill in via `/adr` decisions, customize as needed.

## Next Steps

1. **Review CLAUDE.md** - Add your tech stack and external links
2. **Create Your Vision** - Run `/project-brief`
3. **Start Your First Feature** - Run `/spec`
4. **Learn As You Go** - Commands guide you through the workflow

## Command Reference

| Command | Purpose |
|---------|---------|
| `/toolkit-init` | Initialize project structure |
| `/project-brief` | Create/update project vision |
| `/spec` | Plan features and specs |
| `/adr` | Make technical decisions (ADRs) |
| `/plan TASK-###` | Break down implementation |
| `/implement TASK-### PHASE` | Execute specific phases |
| `/branch` | Branch operations (create, merge, delete, switch) |
| `/commit` | Branch-aware git commits |
| `/quality` | Quality assessment |
| `/test-fix` | Fix failing tests |
| `/project-status` | Project status dashboard |
| `/docs` | Documentation management |

## Specialized Agents

The AI Toolkit includes **19 specialized agents** that automatically activate based on your work:

| Agent | Domain | Auto-Activates For |
|-------|--------|-------------------|
| **brief-strategist** | Product strategy | Project brief development |
| **code-architect** | System design | Architecture decisions, ADRs |
| **ui-ux-designer** | Design & UX | Design decisions, mockups |
| **frontend-specialist** | UI development | Component development |
| **backend-specialist** | Server-side | API implementation, business logic |
| **database-specialist** | Data design | Schema design, query optimization |
| **api-designer** | API architecture | Endpoint design, contracts |
| **test-engineer** | Testing | Test creation, TDD/BDD |
| **code-reviewer** | Code quality | Post-implementation reviews |
| **security-auditor** | Security | Security-critical changes |
| **performance-optimizer** | Performance | Performance bottlenecks |
| **devops-engineer** | Infrastructure | Deployment, CI/CD |
| **technical-writer** | Documentation | Documentation creation |
| **context-analyzer** | Investigation | Bug diagnosis, analysis |
| **project-manager** | Coordination | Multi-agent workflows |
| **refactoring-specialist** | Code cleanup | Technical debt reduction |
| **migration-specialist** | Upgrades | Framework migrations |
| **data-analyst** | Data processing | Analytics, reporting |
| **ai-llm-expert** | AI/ML | AI architecture, LLM integration |

**You don't need to invoke agents manually** - they activate automatically when you use commands like `/implement`, `/adr`, or `/quality`.

## Commands vs Agents: When to Use Which?

Understanding the distinction between **slash commands** and **agents** helps you work more effectively:

### Commands = Structured Workflow Actions

**Use commands when you want:**
- Structured workflows with specific file outputs
- Files created in standard locations with required sections
- Process enforcement and consistency

**Examples:**
```bash
/spec                      # Creates pm/specs/SPEC-###-name.md
/plan TASK-001             # Creates pm/issues/TASK-001-*/PLAN.md
/adr                       # Creates docs/project/adrs/ADR-###.md
/implement TASK-001 1.1    # Executes phase, updates WORKLOG.md
```

Commands orchestrate agents and enforce project structure.

### Agents = Expert Consultation & Automation

**Use agents (via conversation) when you want:**
- Expert advice without creating files
- Quick answers to technical questions
- Exploration without committing to structure

**Examples:**
```
"How should I structure this authentication flow?"
→ backend-specialist provides guidance

"Review this code for security issues"
→ security-auditor analyzes and advises

"What's the best way to handle real-time updates?"
→ backend-specialist + performance-optimizer discuss options
```

Agents provide expertise without enforcing file structure.

### When Both Exist

Some workflows have both command and agent versions:

**`/quality` command:**
- Comprehensive quality report across entire codebase
- Generates quality metrics and analysis
- Use for: Explicit quality audits, pre-release checks

**`code-reviewer` agent (auto-invoked):**
- Per-phase code review during `/implement`
- Provides score (0-100) and iterates until ≥90
- Use for: Automatic quality gates during development

**`/docs` command:**
- Documentation management and synchronization
- Updates docs across entire project
- Use for: Explicit documentation updates

**`technical-writer` agent (auto-invoked):**
- Auto-updates docs when code changes
- Maintains doc-code consistency
- Use for: Automatic documentation during `/implement`

### Rule of Thumb

- **Want a file created?** → Use a command
- **Want expert advice?** → Ask directly (agent activates)
- **Want automated workflow?** → Commands invoke agents automatically

Both approaches work together - commands orchestrate agents to deliver complete workflows.

## Need More Information?

**Ask Claude** - Claude has access to complete plugin documentation and can answer questions:

- "How does the security-auditor agent work?"
- "When should I use the code-architect vs api-designer?"
- "How does the /adr command work?"
- "Show me the full command workflow"
- "What's the difference between /spec and /plan?"

Claude reads the plugin documentation (AGENTS.md, COMMANDS.md) and provides detailed explanations.

## Philosophy

This toolkit embraces AI-assisted development:
- **AI helps you build**, not pre-built templates
- **Structure emerges from work**, not upfront planning
- **Documentation reflects reality**, not aspirations
- **Guidelines are templates**, customized per project

Welcome to AI-assisted development. Let's build something! 🚀
