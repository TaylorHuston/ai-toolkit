---
target_audience: ["ai-assistants", "development-team"]
document_type: "reference"
priority: "high"
tags: ["claude-code", "commands", "workflow", "reference"]
---

# Claude Code Command Reference

Streamlined catalog of 26 Claude Code slash commands centered around the **3-phase development workflow** plus setup, quality, and support commands.

## How Commands Work Together

The AI Toolkit provides a flexible workflow that adapts to your needs:

### Core Development Flow

1. **Brief/One Pager**: `/project-brief` - Define what you're building and why
2. **Planning**: `/epic` - Break features into organized work
3. **Architecture**: `/adr` - Make technical decisions and create ADRs
4. **Tasks**: `/plan TASK-###` - Break down implementation into phases
5. **Build**: `/implement TASK-### PHASE` (automated) or `/advise TASK-### PHASE` (collaborative)
6. **Quality**: `/quality`, `/troubleshoot`, `/security-audit` - Ensure excellence

### Commands Are Conversational

Each command guides you through its workflow:
- `/project-brief` asks questions to complete your proejct brief
- `/epic` helps structure features with acceptance criteria
- `/adr` explores options before creating ADRs
- `/plan` breaks work into testable, reviewable phases
- `/implement` executes with the right domain experts (agents)

### Use Commands Flexibly

You don't have to follow a strict sequence:
- Jump straight to `/adr` for technical decisions
- Use `/implement` to quickly build a spike
- Run `/quality` whenever you want a comprehensive review
- Invoke `/docs` to generate or validate documentation

The workflow is a guide, not a rigid process.

## Command Parameter Patterns

Commands use different parameter paradigms because **different workflows need different interfaces**. This is intentional design, not inconsistency.

### 1️⃣ No Parameters (Conversational)

**When**: Workflow requires Q&A to gather information or explore options

**Commands**: `/toolkit-init`, `/project-brief`, `/adr`

**Why**: These commands guide you through multi-step processes where the AI asks questions to understand your needs before taking action. Parameters would be too rigid.

**Example**:
```bash
/project-brief          # Asks questions to build your project vision
/adr                    # Explores architecture options through conversation
```

### 2️⃣ Required Positional Arguments

**When**: Operating on specific artifacts with clear, predictable inputs

**Commands**: `/plan TASK-###`, `/implement TASK-### PHASE`

**Why**: These commands perform precise operations on identified entities. Required parameters prevent ambiguity and enable validation.

**Example**:
```bash
/plan TASK-001              # Plan section for TASK-001 (clear, unambiguous)
/implement TASK-001 1.2     # Execute phase 1.2 of TASK-001 (specific action)
```

### 3️⃣ Optional Arguments

**When**: Command can operate contextually OR on specific target

**Commands**: `/epic [EPIC-###]`

**Why**: Enables both "create new" and "refine existing" workflows with one command. Parameter is optional because context can determine behavior.

**Example**:
```bash
/epic                   # Create new epic (conversational)
/epic EPIC-001          # Refine existing epic (targeted)
```

### 4️⃣ Subcommand Pattern

**When**: Command provides multiple related operations with distinct behaviors

**Commands**: `/branch {create|merge|delete|switch|status}`, `/quality {assess|validate|audit|fix}`

**Why**: Groups related operations under one command namespace. Each subcommand has its own parameters and workflow.

**Example**:
```bash
/branch create TASK-001     # Create work branch
/branch merge develop       # Merge to staging with validation
/branch status              # Show branch status
```

### 5️⃣ Natural Language Instructions

**When**: Complex operations requiring context, interpretation, or flexibility

**Commands**: `/commit`, `/docs`

**Why**: Some operations benefit from AI understanding intent rather than rigid parameters. Natural language enables nuance.

**Example**:
```bash
/commit "add user authentication with tests"
/docs "sync API documentation with latest endpoint changes"
```

### Pattern Selection Guidelines

**Choose parameter pattern based on workflow needs:**

- **Q&A needed?** → No parameters (conversational)
- **Operating on specific artifact?** → Required positional arguments
- **Context-aware behavior?** → Optional arguments
- **Multiple operations?** → Subcommand pattern
- **Complex intent?** → Natural language

**Not a bug, it's a feature**: Different interfaces optimize for different tasks. Consistency in pattern *selection* (matching interface to workflow) is more valuable than forcing all commands to use the same paradigm.

## 🚀 Setup & Initialization

### 🛠️ **/toolkit-init** - Project Scaffolding

- _Purpose_: Initialize new or existing projects with ai-toolkit structure and templates
- _Usage_: `/toolkit-init`
- _Features_: Interactive customization (2 questions), smart conflict resolution, organized templates (37 files)

### 💡 **/project-brief** - Interactive Project Vision

- _Purpose_: Create or improve project brief through collaborative Q&A
- _Usage_: `/project-brief | /project-brief --force | /project-brief --review`
- _Features_: Gap-driven conversation, section-by-section updates, analysis mode
- _Modes_: Create (new brief), Update (fill gaps), Review (suggestions only)

### 🎯 **/epic** - Unified Epic Management

- _Purpose_: Create new epics or refine existing ones through natural language conversation
- _Usage_: `/epic | /epic EPIC-###`
- _Features_: Context-aware (create or refine), conversational interface, lightweight template
- _Structure_: `pm/epics/EPIC-###-name.md` (single file), `pm/issues/TASK-###-name/` or `BUG-###-name/` (flat directories)

## 🌟 Core Workflow Commands

**3-Phase Development Workflow**: From architecture to validated execution

### 🏗️ **/adr** - Technical Architecture

- _Purpose_: Design technical solutions through Quick Mode (5-10 min) or Deep Mode (20+ min) exploration
- _Usage_: `/adr [--epic EPIC-###] | [--foundation] | [--infrastructure] | [--deep] | [--question "text"]`
- _Workflow Phase_: **1. Architecture** - Technical decisions, ADRs, Fast Track vs comprehensive analysis

### 📋 **/plan** - Task Implementation Planning

- _Purpose_: Create PLAN.md file with phase-based breakdown for individual tasks and bugs
- _Usage_: `/plan TASK-### | /plan BUG-###`
- _Workflow Phase_: **2. Planning** - Phase-based task breakdown, agent coordination, test-first patterns
- _Output_: Creates `pm/issues/TASK-###-*/PLAN.md` (keeps TASK.md clean for PM tool sync)

### ⚡ **/implement** - Automated Phase Execution

- _Purpose_: Execute specific implementation phases from task plans with test-first enforcement (AI writes code)
- _Usage_: `/implement TASK-### PHASE | /implement BUG-### PHASE | /implement --next`
- _Workflow Phase_: **3. Execution (Automated)** - AI writes code, tests, commits
- _Examples_: `/implement TASK-001 1.1`, `/implement BUG-003 2.2`, `/implement --next`

### 🤝 **/advise** - Collaborative Phase Guidance

- _Purpose_: Get implementation guidance for a phase without automated code generation (user writes code)
- _Usage_: `/advise TASK-### PHASE | /advise BUG-### PHASE | /advise --next`
- _Workflow Phase_: **3. Execution (Collaborative)** - AI guides, user codes
- _Examples_: `/advise TASK-001 1.2`, `/advise --next`
- _Hybrid_: Mix `/implement` (AI codes) and `/advise` (user codes) per phase

## 🔧 Supporting Commands

### **Quality & Security**

- **[/quality](./quality.md)** - Multi-dimensional quality analysis using specialized agents
- **[/security-audit](./security-audit.md)** - OWASP-compliant security assessment with vulnerability remediation
- **[/troubleshoot](../commands/troubleshoot.md)** - Systematic debugging with research-first approach, hypothesis testing, and rollback safety
- **[/sanity-check](../commands/sanity-check.md)** - Mid-work validation with deep reflection to catch drift before it becomes expensive

### **Development Support**

- **[/branch](../commands/branch.md)** - Unified branch operations (create, merge, delete, switch, status) with git-workflow enforcement
- **[/commit](../commands/commit.md)** - Branch-aware git commits with automatic issue references
- **[/worklog](../commands/worklog.md)** - Add timestamped work log entries for human-AI collaboration
- **[/sync-progress](../commands/sync-progress.md)** - Analyze git changes, update plan to reflect progress, and document in WORKLOG
- **[/refresh](../commands/refresh.md)** - Silently refresh AI context by reading project configuration, guidelines, and recent commits

### **Jira Integration**

- **[/import-issue](../commands/import-issue.md)** - Import Jira issue for local work with PLAN.md creation
- **[/promote](../commands/promote.md)** - Promote local exploration issue to Jira for team visibility
- **[/comment-issue](../commands/comment-issue.md)** - Add AI-suggested comments to external Jira issues based on work context
- **[/refresh-schema](../commands/refresh-schema.md)** - Refresh Jira field schema cache when requirements change

### **Project Management & Documentation**

- **[/project-status](../commands/project-status.md)** - Project status dashboard with intelligent context analysis
- **[/docs](./docs.md)** - Unified documentation management (generate, validate, sync, update, health)

### **Versioning & Releases**

- **[/changelog](../commands/changelog.md)** - Check and update CHANGELOG.md with undocumented changes
- **[/release](../commands/release.md)** - Release new version following semantic versioning guidelines

---

## 📚 Complete Command Index

| Command | Purpose | Usage Pattern |
|---------|---------|---------------|
| `/toolkit-init` | Project scaffolding | Interactive (2 questions) |
| `/project-brief` | Interactive project vision | `[--force] [--review]` |
| `/epic` | Unified epic management | `[EPIC-###]` (optional) |
| `/adr` | Technical architecture | Various flags for modes |
| `/plan` | Task implementation planning | `TASK-###` or `BUG-###` |
| `/implement` | Automated phase execution | `TASK-### PHASE` or `--next` |
| `/advise` | Collaborative phase guidance | `TASK-### PHASE` or `--next` |
| `/quality` | Quality assessment | Subcommands (assess/validate/audit/fix) |
| `/security-audit` | Security assessment | OWASP compliance analysis |
| `/troubleshoot` | Systematic debugging | `[BUG-XXX \| TASK-XXX] [--continue]` |
| `/sanity-check` | Mid-work validation | No arguments (deep reflection) |
| `/branch` | Branch operations | `create \| merge \| delete \| switch \| status` |
| `/commit` | Branch-aware git commits | Natural language instructions |
| `/worklog` | Work log entries | `"your comment text"` |
| `/sync-progress` | Sync plan with git changes | No arguments (analyzes diff) |
| `/refresh` | Refresh AI context | No arguments (silent) |
| `/import-issue` | Import Jira issue | `PROJ-###` |
| `/promote` | Promote local to Jira | `TASK-### \| BUG-###` |
| `/comment-issue` | Add Jira comment | `PROJ-### ["text"]` |
| `/refresh-schema` | Refresh Jira schema | No arguments |
| `/project-status` | Project dashboard | `[--format] [--scope] [--detailed]` |
| `/docs` | Documentation management | Natural language instructions |
| `/changelog` | CHANGELOG maintenance | No arguments (interactive) |
| `/release` | Version releases | `[version] \| patch \| minor \| major` |

## Command Creation Best Practices

### Command Structure Template

**YAML Frontmatter:**
```yaml
---
allowed-tools: ["Read", "Grep", "Glob"]  # Essential tools only
argument-hint: "[target] [mode] | [\"natural language instruction\"]"  # Expected arguments
description: "Brief command purpose"     # One-line description
model: claude-sonnet-4-5                 # Versioned alias (claude-sonnet-4-5, claude-opus-4-0)
---
```

**Content Structure:**
```markdown
## /command-name Command

**Purpose**: Clear, concise purpose statement

## Usage
```bash
/command-name target-name         # Basic usage
/command-name target-name mode    # With mode
/command-name "direct question"   # Direct question
```

### Claude Code Argument System

**Supported Patterns:**
- **Positional**: `$1`, `$2`, `$3` for individual arguments
- **Natural Language**: `$ARGUMENTS` for flexible instructions
- **NOT supported**: `--flag` syntax (use positional instead)

**Usage Examples:**
```bash
# Positional arguments (structured commands)
/command epic-name        # $1="epic-name"
/command epic-name deep   # $1="epic-name", $2="deep"

# Natural language (flexible commands)
/command "all files with message 'feat: add auth'"     # $ARGUMENTS
/command "only the components we changed for this task" # $ARGUMENTS
```

**When to Use Each:**
- **Positional**: Simple, structured commands with predictable patterns
- **Natural Language**: Complex instructions requiring context and intelligence

## Arguments _(choose approach)_

**Positional Arguments** _(for structured commands)_:
| Position | Type | Values | Description |
|----------|------|--------|-------------|
| `$1` | string | target-name | Primary target |
| `$2` | string | mode | Optional mode |

**Natural Language** _(for flexible commands)_:
| Variable | Type | Description |
|----------|------|-------------|
| `$ARGUMENTS` | string | Full natural language instruction |

## Agent Coordination _(if multi-agent)_
**Primary**: agent-name **Supporting**: agent1, agent2

## Context _(if needed)_
[Files to read for context - e.g., project docs, existing patterns]

## Instructions
1. Step-by-step numbered list
2. Clear, actionable items
3. Expected outcomes

## Output
[What to return to the user when done]

**Related**: Previous → **This** → Next _(if part of workflow)_
```

### **🤖 Model Selection Strategy**

#### **Claude Opus 4** - Complex Reasoning & Architecture

- **Use For**: Architecture, planning, security analysis, strategic decisions
- **Model ID**: `claude-opus-4-0` (versioned alias)
- **Commands**: `/adr`, `/plan`, `/security-audit`
- **When**: Complex multi-step reasoning, architectural decisions, strategic planning

#### **Claude Sonnet 4.5** - Execution & Development

- **Use For**: Development, quality, documentation, operations, most commands
- **Model ID**: `claude-sonnet-4-5` (versioned alias)
- **Commands**: `/implement`, `/commit`, `/quality`, `/troubleshoot`, `/docs`, `/project-status`, `/epic`, `/project-brief`
- **When**: Implementation tasks, code execution, standard operations

#### **Model Selection Guidelines**

- **Complex Strategy**: Use Opus 4 for decisions requiring deep reasoning
- **Implementation Focus**: Use Sonnet 4.5 for coding and execution tasks
- **Default**: Sonnet 4.5 for most commands unless deep reasoning required

### Tool Permissions Guidelines

**Essential Tools**: `Read`, `Grep`, `Glob` - Always safe for file operations

**Modification Tools**: `Edit` (preferred), `MultiEdit`, `Write` (use sparingly)

**Execution Tools**: `Bash` (specify scope when possible)

**Advanced Tools**: `Task` (multi-agent), `TodoWrite` (progress tracking)

```yaml
# Simple commands
allowed-tools: ["Read", "Grep", "Glob"]

# Commands with modifications
allowed-tools: ["Read", "Edit", "Bash", "Grep", "Glob"]

# Complex multi-agent commands
allowed-tools: ["Read", "Edit", "Bash", "Grep", "Glob", "Task", "TodoWrite"]
```

### Quality Guidelines

**Performance Expectations**:

- Fast commands (<30s): Status, simple operations
- Standard commands (1-5min): Code generation, analysis
- Complex commands (5-15min): Multi-agent coordination

**Security & Best Practices**:

- Use principle of least privilege for tool permissions
- Prefer `Read` over `Write`, `Edit` over `MultiEdit`
- Minimize context loading, use progressive information gathering
- Validate inputs and handle errors gracefully

**Quality Checklist**:

- [ ] YAML front matter validates correctly
- [ ] All specified tools are actually used
- [ ] Works with various argument combinations
- [ ] Clear purpose and predictable behavior
- [ ] Practical examples and proper documentation
- [ ] Appropriate model assignment for complexity

This guide ensures all commands follow Claude Code best practices while maintaining consistency and quality.
