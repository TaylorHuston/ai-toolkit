# Getting Started with AI Toolkit

Welcome! You've initialized your project with the AI Toolkit for Claude Code.

## Your Project Structure

```
your-project/
├── CLAUDE.md               # Project context for AI
├── README.md               # Project overview
├── GETTING-STARTED.md      # This file
├── CHANGELOG.md            # Version history
├── docs/
│   ├── project-brief.md    # Your vision (start here!)
│   ├── project/            # Architecture, ADRs, design assets
│   └── development/        # Customizable guidelines and templates
│       ├── conventions/    # 7 project conventions
│       ├── workflows/      # 9 AI execution protocols
│       ├── templates/      # 12 PM & doc templates
│       └── misc/           # 4 reference docs (commands, agents, integrations)
└── pm/
    ├── specs/              # Feature planning (created by /spec)
    └── issues/             # Tasks and bugs (created by /plan)
```

## Quick Start

1. **`/project-brief`** - Create your Project Brief through an interactive session. This is the high-level, relatively non-technical overview of your project: the problem you're solving, your target audience, core features. Think "One Pager" or "Elevator Pitch" - the guiding direction for everything else.

2. **`/adr`** - Create Architecture Decision Records for technical decisions ("What database should I use?", "Deploy to Vercel or Netlify?"). ADRs are intended to be static once approved - if something needs changing, create a new ADR to supersede it.

3. **`/spec`** - Create a Spec Document (the heart of the workflow). A Spec is a concrete plan for a body of related work - similar in scope to an Epic in Agile or a PRD. Written for easy parsing by Claude Code with clear requirements, acceptance criteria, and definition of done. Unlike ADRs, Specs are living documents that adapt as you discover hiccups and make adjustments during implementation.

4. **`/plan TASK-###`** - Create a thorough implementation plan with all steps needed to complete the task. In the default configuration, this breaks work into discrete Phases (each a logical commit with strict TDD workflow), but you can customize to your specifications.

5. **`/implement TASK-### PHASE`** - Start implementing. Execute a single phase or let Claude complete the entire task end-to-end. Quality gates and workflows make autonomous execution both possible and safe, but you're always in control.

## Commands, Workflows and Conventions

The AI Toolkit provides **25+ commands** that follow workflows in your `docs/development/workflows/` directory and respect conventions in `docs/development/conventions/`.

**Why file-based configuration?** Commands are intentionally minimal - they read your project's guideline files to adapt behavior. Baseline versions come with the toolkit, but keeping them as files in your repo means you can customize them to fit your team's specific workflow and conventions.


## Default Implementation Loop

The `/implement` command follows a strict **test-first** workflow defined in `docs/development/workflows/development-loop.md`:

### Phase Structure

Each phase follows this mandatory loop:

1. **Write Tests First** (TDD)
   - Write failing tests that define the feature
   - No implementation until tests exist
   - Tests serve as executable specifications

2. **Implement**
   - Write minimum code to make tests pass
   - Follow coding standards from `docs/development/conventions/`
   - Use appropriate design patterns

3. **Code Review** (Automatic)
   - `code-reviewer` agent analyzes the code
   - Scores 0-100 on quality metrics
   - Must achieve ≥90 to proceed
   - Iterates until quality threshold met

4. **Verify Tests Pass**
   - All tests must pass
   - No regressions allowed
   - Phase blocks if tests fail

5. **Update Documentation**
   - `technical-writer` agent updates relevant docs
   - Ensures code-doc synchronization
   - Maintains accuracy

6. **Log Work**
   - Updates `WORKLOG.md` with phase completion
   - Documents decisions and discoveries
   - Tracks progress

**This loop is enforced for every phase** - you can customize it in `docs/development/workflows/development-loop.md`.

### Quality Gates

Multiple quality gates ensure high standards:

- **Test Coverage**: Required % defined in `docs/development/conventions/testing-standards.md`
- **Code Review Score**: ≥90/100 (configurable in `docs/development/workflows/quality-gates.md`)
- **Security Checks**: Automatic for auth, data handling, secrets
- **Performance**: Checks for N+1 queries, inefficient patterns

### Phase Execution

```bash
/implement TASK-001 1
# → Executes phase 1, reviews, tests
# → Waits for your instructions to proceed to the next phase
```

### Autonomous Execution

With `--task` flag, `/implement` can execute all phases autonomously:

```bash
/implement --task TASK-001
# → Executes phase 1, reviews, tests
# → If passes, automatically starts phase 2
# → Continues until all phases complete or failure
```

Quality gates make this safe - work stops automatically if standards aren't met.

## Default Git Workflow

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
/implement TASK-001 1
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

Your git workflow is defined in `docs/development/workflows/git-workflow.md`:
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
Your project includes **16 customizable guideline files** organized in 4 directories:

**Conventions** (`docs/development/conventions/` - 7 files):
- `api-guidelines.md` - API patterns and structure
- `architectural-principles.md` - Design philosophy
- `coding-standards.md` - Code style and conventions
- `security-guidelines.md` - Security practices
- `testing-standards.md` - Testing frameworks, coverage, strategy
- `ui-design-guidelines.md` - Design tokens and UI patterns
- `versioning-and-releases.md` - Semantic versioning, releases, CHANGELOG

**Workflows** (`docs/development/workflows/` - 9 files):
- `agent-coordination.md` - How specialized agents work together
- `development-loop.md` - AI-assisted workflow and quality gates
- `git-workflow.md` - Branching, commits, PRs, releases
- `pm-file-formats.md` - SPEC.md, TASK.md, PLAN.md formats
- `pm-workflows.md` - Planning and implementation workflows
- `quality-gates.md` - Quality standards and gates
- `troubleshooting.md` - Debugging workflows
- `worklog-examples.md`, `worklog-format.md` - WORKLOG.md structure

**Templates** (`docs/development/templates/` - 12 files): PM and documentation templates

**Misc** (`docs/development/misc/` - 4 files): Command/agent references and integration guides

Most guidelines start with TBD placeholders - fill in via `/adr` decisions and customize as needed.

## Next Steps

1. **Review CLAUDE.md** - Add your tech stack and external links
2. **Create Your Vision** - Run `/project-brief`
3. **Start Your First Feature** - Run `/spec`
4. **Learn As You Go** - Commands guide you through the workflow

## Keeping Templates Updated

The AI Toolkit plugin regularly improves its guidelines, workflows, and templates. Keep your project in sync:

### Check for Updates

```bash
# Preview what would change (safe, no modifications)
/toolkit-init --dry-run
```

This shows:
- New guideline files added
- Improvements to existing templates
- Bug fixes to workflows
- New PM templates

### Apply Updates

```bash
# Smart update - preserves your customizations
/toolkit-init
```

**Preserved:**
- ✅ Your customized guidelines
- ✅ Your project-specific docs (ADRs, design docs, etc.)
- ✅ All PM files (specs, tasks, bugs, plans, worklogs)
- ✅ CLAUDE.md and README.md customizations

**Updated:**
- ✅ New guideline files you don't have yet
- ✅ Baseline templates (you can re-customize)
- ✅ Workflow improvements
- ✅ Bug fixes

### Complete Reset

```bash
# Nuclear option - overwrites ALL customizations
/toolkit-init --force
```

⚠️ **Warning:** Only use `--force` if you want to completely reset to baseline templates. All customizations will be lost.

### Best Practice

After updating the AI Toolkit plugin:

1. Run `/toolkit-init --dry-run` to preview changes
2. Review what's new
3. Run `/toolkit-init` to apply updates
4. Re-apply any customizations if needed (compare with git diff)

## Command Reference

**24 commands organized by workflow stage:**

### Setup & Strategy
| Command | Purpose |
|---------|---------|
| `/toolkit-init` | Initialize project or sync latest templates (see "Keeping Templates Updated" above) |
| `/project-brief` | Create/update project vision |

### Spec & Epic Management
| Command | Purpose |
|---------|---------|
| `/spec` | Create feature specs with acceptance criteria |
| `/jira-epic` | Create epics in Jira (requires optional integration) |
| `/jira-import` | Import Jira issues for local work |

### Planning & Implementation
| Command | Purpose |
|---------|---------|
| `/adr` | Make architecture decisions and create ADRs |
| `/plan TASK-###` | Break down implementation into phases |
| `/implement TASK-### PHASE` | Execute specific phases with agents |
| `/advise TASK-### PHASE` | Get implementation guidance without code generation |

### Quality & Security
| Command | Purpose |
|---------|---------|
| `/quality` | Multi-dimensional quality assessment, a good best practice is to run this once a Task is complete before you merge |
| `/security-audit` | OWASP-compliant security assessment |
| `/troubleshoot` | Structured 5-step troubleshooting workflow |
| `/sanity-check` | Mid-work validation and alignment check, run if you feel like Claude has hit a wall and/or is going down the wrong path |

### Development Support
| Command | Purpose |
|---------|---------|
| `/branch` | Branch operations (create, merge, delete, switch, status) |
| `/commit` | Branch-aware git commits with issue references |
| `/worklog` | Manually add timestamped work log entries |
| `/sync-progress` | Sync project state after manual changes, run if you've made changes on your own and want Claude to be made aware of them and update the Plan if necessary |

### Documentation & Status
| Command | Purpose |
|---------|---------|
| `/docs` | Unified documentation management |
| `/project-status` | Project status dashboard |
| `/changelog` | Check and update CHANGELOG.md |
| `/release` | Release new version with semantic versioning |

### Utilities
| Command | Purpose |
|---------|---------|
| `/refresh` | Silently refresh AI context, good to run periodically to make sure all of the important context (CLAUDE.md, etc) is fresh |
| `/ui-design` | Create HTML UI mockups with parallel variants |
| `/jira-comment` | Add AI-suggested comments to Jira issues |
| `/jira-promote` | Promote local exploration issues to Jira |

**Full reference**: See `docs/development/misc/commands.md` for complete documentation with examples.

## Specialized Agents

The AI Toolkit includes **21 specialized agents** that automatically activate based on your work:

| Agent | Domain | Auto-Activates For |
|-------|--------|-------------------|
| **brief-strategist** | Product strategy | Project brief development |
| **code-architect** | System design | Architecture decisions, ADRs |
| **project-manager** | Coordination | Multi-agent workflows, complex features |
| **ui-ux-designer** | Design & UX | Design decisions, mockups |
| **frontend-specialist** | UI development | Component development, React/Vue/etc |
| **backend-specialist** | Server-side | API implementation, business logic |
| **database-specialist** | Data design | Schema design, query optimization |
| **api-designer** | API architecture | Endpoint design, contracts |
| **test-engineer** | Testing | Test creation, TDD/BDD |
| **code-reviewer** | Code quality | Post-implementation reviews |
| **security-auditor** | Security | Security-critical changes, audits |
| **performance-optimizer** | Performance | Performance bottlenecks, optimization |
| **devops-engineer** | Infrastructure | Deployment, CI/CD, containerization |
| **technical-writer** | Documentation | Documentation creation and sync |
| **context-analyzer** | Investigation | Bug diagnosis, research, analysis |
| **refactoring-specialist** | Code cleanup | Technical debt reduction |
| **migration-specialist** | Upgrades | Framework migrations, dependency updates |
| **ai-llm-expert** | AI/ML | AI architecture, LLM integration |
| **aws-expert** | AWS Cloud | AWS architecture, service selection |
| **azure-expert** | Azure Cloud | Azure architecture, service selection |
| **gcp-expert** | Google Cloud | GCP architecture, service selection |

**You don't need to invoke agents manually** - they activate automatically when you use commands like `/implement`, `/adr`, or `/quality`.

**Full reference**: See `docs/development/misc/agents.md` for complete agent documentation.

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

Claude reads the plugin documentation from `docs/development/misc/agents.md` and `docs/development/misc/commands.md` to provide detailed explanations.

**Documentation locations:**
- **Commands**: `docs/development/misc/commands.md` - Complete command reference
- **Agents**: `docs/development/misc/agents.md` - Agent catalog and system overview
- **Workflows**: `docs/development/workflows/` - AI execution protocols
- **Conventions**: `docs/development/conventions/` - Project conventions and standards
- **Templates**: `docs/development/templates/` - PM and documentation templates

## Philosophy

This toolkit embraces AI-assisted development:
- **AI helps you build**, not pre-built templates
- **Structure emerges from work**, not upfront planning
- **Documentation reflects reality**, not aspirations
- **Guidelines are templates**, customized per project

Welcome to AI-assisted development. Let's build something! 🚀
