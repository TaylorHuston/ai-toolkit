# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.11.1] - 2025-11-02

### Added

- **Automatic Security Reviews**: Security-auditor agent now provides mandatory sign-off on security-relevant code
  - **Plan-level review**: Security-auditor reviews implementation plans for tasks involving authentication, authorization, encryption, PII handling, or API integrations
  - **Phase-level review**: Automatic security review before completing phases that modify security-sensitive code
  - **Auto-detection**: Keywords (`auth`, `login`, `password`, `token`, `encrypt`, `crypto`, `PII`, `OAuth`, etc.) and file patterns (`**/auth/**`, `**/security/**`, `**/*Auth*`, etc.) trigger security reviews
  - **Quality gates**: Security review blocks phase/plan completion if critical vulnerabilities found
  - **OWASP compliance**: Reviews check for injection vulnerabilities, XSS, CSRF, proper cryptographic usage, input validation, and authorization logic
  - **Zero overhead**: Non-security tasks skip security review automatically
  - Commands affected: `/plan` (conditional review after code-architect), `/implement` (conditional review before phase completion)
  - **Extended coverage**: Added security-auditor references to `/epic` (flags security-relevant work types), `/test-fix` (validates security test coverage), `/docs` (validates security documentation accuracy)

- **PM Guidelines**: New `pm-guidelines.md` guideline for epic and issue management patterns
  - **Machine-readable configuration**: YAML frontmatter with epic/issue structure, numbering patterns, Jira integration rules
  - **Comprehensive patterns**: Epic creation, task breakdown, naming conventions, Jira field discovery, promotion workflows
  - **Single source of truth**: All PM patterns extracted from `/epic` command to guideline (following command simplification pattern)
  - **Customizable**: Teams can adjust epic sizes, issue types, Jira mappings in one location

- **Agent Coordination Guidelines**: New `agent-coordination.md` guideline for agent governance and collaboration
  - **Architectural governance**: Documents code-architect mandatory review authority and workflow
  - **Security governance**: Documents security-auditor auto-invocation patterns, detection criteria, blocking authority
  - **Quality gates**: Documents code-reviewer thresholds, test-engineer coverage requirements
  - **Escalation paths**: Clear rules for when agents escalate to other agents (architectural questions, security concerns, performance issues)
  - **Collaboration patterns**: Documents parallel work, sequential handoff, and continuous collaboration patterns
  - **Responsibility boundaries**: Clarifies overlapping domains (performance optimization, code improvement, analytics)
  - **Customizable**: Teams can adjust quality thresholds, security detection keywords, escalation rules
  - **Agent updates**: 5 domain specialists (frontend, backend, database, api-designer, devops) now reference coordination guideline

### Changed

- **Command Cohesion Improvements**: Extended "orchestrate, don't prescribe" pattern across all commands
  - **`/epic` command**: 457 → 266 lines (42% reduction) - extracted patterns to `pm-guidelines.md`
  - **Guideline references**: Added `references_guidelines` frontmatter to `/epic`, `/project-brief`, `/docs`, `/project-status` for better dependency tracking
  - **Consistent pattern**: All workflow commands now reference guidelines instead of duplicating rules

## [0.11.0] - 2025-10-31

### Added

- **Jira Integration Support**: Optional Jira integration for teams using Atlassian Jira
  - **Hybrid workflow**: Local exploration (TASK-###, BUG-###) + Jira issues (PROJ-###) coexist
  - **Epics in Jira**: When Jira enabled, epics only created in Jira (no local epic files)
  - **Field discovery**: Automatic Jira field schema discovery with conversational collection
  - **Field caching**: `.ai-toolkit/jira-field-cache.json` caches field requirements
  - **Atlassian Remote MCP**: Integration via official Atlassian Remote MCP Server
  - **Read-only status**: Display Jira status in project-status (manual updates in UI)
  - **On-demand fetch**: Fresh fetch from Jira for requirements (no local TASK.md for Jira issues)

- **`/import-issue` command**: Pull Jira issues into local project structure for AI-assisted work
  - Import with `/import-issue PROJ-123`
  - Creates `pm/issues/PROJ-123-{name}/` directory
  - Displays issue details from Jira
  - Optional - `/plan` auto-imports if needed
  - Useful for previewing issue before planning

- **`/promote` command**: Promote local exploration issues to Jira for team collaboration
  - Promote with `/promote TASK-001`
  - Reads local TASK.md or BUG.md
  - Discovers Jira fields with conversational prompts
  - Creates issue in Jira via Atlassian MCP
  - Migrates PLAN.md, WORKLOG.md, RESEARCH.md to Jira issue directory
  - Optional cleanup of original directory
  - Updates git branch (feature/TASK-001 → feature/PROJ-123)

- **`/refresh-schema` command**: Refresh Jira field schema cache when requirements change
  - Clears `.ai-toolkit/jira-field-cache.json`
  - Fetches fresh field metadata from Jira
  - Updates cache with new schema
  - Use when Jira administrators add new required fields
  - Validates all issue types (Epic, Story, Task, Bug)

### Changed

- **Command Simplification (KISS Philosophy Restored)**: Major refactoring of `/plan` and `/implement` commands
  - **Problem**: Commands had grown to 363 and 473 lines, duplicating content from guidelines, violating DRY and Single Source of Truth principles
  - **Solution**: Moved prescriptive workflow details to `development-loop.md` guideline where teams can customize
  - **`/plan` command**: 363 → 209 lines (42% reduction)
  - **`/implement` command**: 473 → 237 lines (50% reduction)
  - **`development-loop.md` guideline**: Added 6 new sections with moved content:
    - Implementation Plan Structure (phase templates by task type)
    - Plan Review Requirements (code-architect mandatory review details)
    - Progress Tracking Protocol (checkbox management for PLAN.md and TASK.md)
    - Task Completion Validation (comprehensive completion checklist)
    - Test-First Guidance Protocol (pragmatic test-first prompting logic)
    - Agent Context Briefing (domain-specific context filtering patterns)
  - **Benefits**: Single Source of Truth (edit guideline once, affects all commands), easier team customization, faster AI processing, better maintainability
  - **Philosophy**: Commands orchestrate (invariant), guidelines configure (customizable)

- **`/plan` command**: Dual-mode support for local and Jira issues with mandatory architectural review
  - ID detection: TASK-###/BUG-### (local) vs PROJ-### (Jira)
  - Local workflow: Read from TASK.md/BUG.md (unchanged)
  - Jira workflow: Fetch from Jira, no local TASK.md, auto-import if needed
  - Creates PLAN.md for implementation phases in both modes
  - **Mandatory code-architect signoff**: code-architect agent reviews architectural soundness, consistency with ADRs, phase structure, and cross-cutting concerns before plan presented to user
  - Ensures architectural oversight on every implementation plan

- **`/epic` command**: Jira epic creation with field discovery
  - Local mode: Creates `pm/epics/EPIC-###-name.md` (when Jira disabled)
  - Jira mode: Creates epic in Jira with conversational field collection (when Jira enabled)
  - Field caching: Stores discovered schema in `.ai-toolkit/jira-field-cache.json`
  - No local epic files when Jira enabled

- **`/implement` command**: Enhanced progress tracking with checkbox management and Jira support
  - ID detection: TASK-###/BUG-### (local) vs PROJ-### (Jira)
  - Local workflow: Read from TASK.md/BUG.md (unchanged)
  - Jira workflow: Fresh fetch from Jira, read PLAN.md locally
  - Branch naming: feature/PROJ-123, bugfix/PROJ-456
  - **Mandatory progress tracking**: Checks off PLAN.md phases and TASK.md acceptance criteria as work completes
  - **Verification before checkoff**: Only marks items complete after tests pass and requirements verified
  - **Immediate updates**: Updates checkboxes after each phase completion, not in batches
  - **Acceptance criteria tracking**: Marks off criteria in TASK.md as phases satisfy them
  - **Task completion validation**: Ensures ALL phases and criteria checked before marking task complete
  - Clear separation: PLAN.md tracks implementation phases, TASK.md tracks acceptance criteria satisfaction

- **`/project-status` command**: Hybrid status display for Jira and local issues
  - Jira disabled: Show local epics and issues only (unchanged)
  - Jira enabled: Show Jira epics, Jira issues with local status, local exploration
  - Status correlation: Match Jira issues with local PLAN.md/WORKLOG.md
  - Divergence detection: Warn when local complete but Jira shows in progress

- **README.md**: Added comprehensive Jira integration documentation
  - "Local Project Management" section explaining local-only workflow
  - "Working With Jira" section with setup, workflows, limitations, troubleshooting
  - Requirements, common workflows, and integration patterns

- **CLAUDE.md template**: Added Jira configuration section
  - Configuration: jira.enabled (true/false), project key, MCP server
  - How it works: Epics only in Jira, issues Jira or local, local storage strategy
  - Commands: /epic, /plan, /import-issue, /promote, /refresh-schema
  - Field discovery: Automatic schema discovery with conversational prompts

- **Standardized YAML frontmatter across all template files**: Consistent metadata structure for AI agents
  - Added `# === Metadata ===` section to all 16 template files
  - Standardized fields: template_type, version, created, last_updated, status, target_audience, description
  - **7 guideline files** (git-workflow.md, development-loop.md, api-guidelines.md, coding-standards.md, security-guidelines.md, testing-standards.md, architectural-principles.md)
    - Added metadata header while preserving existing configuration sections
    - Configuration sections renamed with `# === [Type] Configuration ===` headers
  - **5 PM template files** (task.md, bug.md, epic.md, plan.md, adr-template.md)
    - Added metadata header with `# === Template Configuration ===` for existing fields
  - **3 documentation files** (architecture-overview.md, design-system.md, writing-style.md)
    - Reorganized existing frontmatter with metadata header and consistent field order
  - **1 project brief** (project-brief.md)
    - Added frontmatter (previously had none)
  - **Benefits**: Consistent metadata parsing, better AI agent context, standardized template versioning

## [0.10.5] - 2025-10-30

### Added

- **Git workflow emergency hotfix documentation**: Rare exception process for critical production fixes
  - Hotfix branches can bypass staging with ADR justification
  - Requires immediate backport to develop after main deployment
  - Post-mortem documentation explaining why normal flow wasn't possible
  - Should be rare (<5% of cases) - most "urgent" bugs can wait for staging
  - Added comprehensive documentation to git-workflow.md

- **`plan.md` template**: New template for AI-managed implementation plans (pm/templates/plan.md)
  - Defines PLAN.md structure with phases, complexity analysis, alternative patterns
  - Used by `/plan` command to create separate PLAN.md files
  - Includes YAML frontmatter for metadata, complexity scoring, timestamps
  - Starter template now includes 34 files (was 33 in v0.10.4)

### Changed

- **Git workflow documentation clarified**: Strengthened language around production merge protection
  - **Branch Merge Rules** section added to git-workflow.md
  - Made explicit: Work branches (feature/*, bugfix/*) MUST merge to develop ONLY
  - Made explicit: ONLY develop can merge to main (except emergency hotfixes)
  - Added ❌/✅ visual indicators for correct/incorrect merge paths
  - Updated /branch command documentation with enforcement details
  - Updated CLAUDE.md, GETTING-STARTED.md with clearer rules
  - Changed "should" to "MUST" and "BLOCKS" throughout for critical rules

- **BREAKING: `/plan` now creates PLAN.md file instead of Plan section in TASK.md**
  - Separates AI-managed implementation details from PM-tool-synced requirements
  - TASK.md stays clean for syncing with Jira, Linear, GitHub Issues
  - PLAN.md contains phase-based breakdown with checkboxes
  - `/implement` reads from PLAN.md instead of TASK.md Plan section
  - Command version bumped to 0.6.0
  - **Migration**: Manually move existing Plan sections from TASK.md/BUG.md to new PLAN.md files
  - **Benefits**: No conflicts between external PM updates and internal planning

- **task.md template**: Removed Plan section (now in separate PLAN.md)
  - Added note: "Run `/plan TASK-{id}` to create PLAN.md"
  - Cleaner structure for PM tool synchronization

- **bug.md template**: Removed Fix Plan section (now in separate PLAN.md)
  - Added note: "Run `/plan BUG-{id}` to create PLAN.md"
  - Cleaner structure for PM tool synchronization

- **pm/templates/README.md**: Updated with PLAN.md template documentation
  - Added "Available Templates" section listing all 4 templates
  - Added "PLAN.md Template" section explaining separation rationale
  - Documents why PLAN.md is separate from TASK.md/BUG.md

## [0.10.4] - 2025-10-30

### Changed

- **BREAKING: `/status` renamed to `/project-status`**: Avoid conflict with built-in Claude Code `/status` command
  - All references updated across documentation
  - Command version bumped to 0.4.0
  - Usage: `/project-status` instead of `/status`
  - Users must update any scripts or documentation referencing the old command name

## [0.10.3] - 2025-10-30

### Added

- **`design-system.md` template**: Visual design source of truth for starter template (532 lines)
  - Comprehensive UI/UX design system documentation
  - Foundation (colors, typography, spacing, layout, shadows, borders)
  - Component specifications (buttons, forms, cards, modals, navigation, tables)
  - Interaction patterns (animations, micro-interactions, loading states)
  - Accessibility standards (WCAG 2.1 AA, keyboard nav, screen readers)
  - Responsive design and dark mode specifications
  - Design tokens and implementation guidelines
  - Positioned as visual design equivalent of `architecture-overview.md`

- **`writing-style.md` template**: Optional content/copywriting guidelines (443 lines)
  - Voice and tone guidelines for brand consistency
  - Microcopy patterns (buttons, errors, forms, empty states, notifications)
  - Capitalization, formatting, and grammar conventions
  - Accessibility considerations (plain language, screen reader text)
  - Marked as optional - for projects with user-facing content needs
  - Starter template now includes 33 files (was 31 in v0.10.2)

### Changed

- **`design/README.md` updated**: Added references to new design documentation
  - Links to `design-system.md` (required visual design spec)
  - Links to `writing-style.md` (optional content guidelines)
  - Clear separation: specifications vs. assets vs. implementation

## [0.10.2] - 2025-10-30

### Added

- **`development-loop.md` guideline**: 7th guideline template added to starter template (826 lines)
  - Comprehensive AI-assisted development workflow guide
  - Documents WORKLOG.md and RESEARCH.md usage patterns
  - Test coverage targets (95% default), quality gates, and best practices
  - Starter template now includes 31 files (was 30 in v0.10.1)

### Removed

- **`template-maintainer` agent**: Removed from agent roster (20 → 19 agents)
  - Agent count reduced to 19 specialized domain experts
  - Updated all documentation to reflect accurate agent count

### Changed

- **BREAKING: `/architect` renamed to `/adr`**: Simplified command with natural language interface
  - **Old**: Complex flags (`--question`, `--epic`, `--foundation`, `--infrastructure`, `--deep`)
  - **New**: Natural language only (`/adr`, `/adr "database selection"`)
  - Removed Direct Question mode (just ask questions in normal conversation)
  - Removed mode distinctions (Quick/Deep) - all ADRs created through same conversational flow
  - Command ALWAYS creates an ADR through interactive conversation
  - **8-step process**: Read Context → Understand → Ask → Present → Discuss → Confirm → Create ADR → Update Architecture Overview
  - **Comprehensive context reading**: Read best practices, template, **all existing ADRs**, and architecture overview before conversation
  - **Existing ADRs inform decisions**: Understand decision history, avoid conflicts, reference related decisions, build on previous choices
  - **Architecture overview integration**: Read `architecture-overview.md` at start to understand current architecture, update at end to reflect new decision
  - **Quality over speed**: Emphasis on taking time, thinking thoroughly, considering long-term implications (1/3/5 years)
  - **One question at a time**: Never present wall of questions, build understanding progressively
  - **Leverage specialist agents**: Consult database-specialist, devops-engineer, security-auditor, etc. for expert analysis
  - Streamlined command file: 407 lines → 197 lines (52% reduction)
  - Concrete example conversation showing one-at-a-time questioning

- **`/branch` command simplified**: Moved workflow rules to guideline, reduced prescriptive content
  - Streamlined command file: 519 lines → 148 lines (71% reduction)
  - Moved workflow rules, validation details, test execution to `git-workflow.md` guideline
  - Command now references guideline as **source of truth** for all branching rules
  - Proper separation of concerns: guidelines define rules, commands enforce them
  - Eliminated duplication between command and guideline
  - Command emphasizes: "Always read git-workflow.md FIRST"
  - Clearer focus: command describes WHAT operations do, guideline defines HOW and WHY

- **`/epic` command simplified**: Moved epic patterns and best practices to guideline, removed verbose content
  - Streamlined command file: 694 lines → 242 lines (65% reduction)
  - Removed: Verbose creation/refinement flows (210 lines), multiple examples (143 lines), tips/success criteria/common use cases
  - Moved to: `pm/README.md` (epic patterns, best practices, file organization, ID numbering)
  - Command now references `pm/templates/epic.md` and `pm/README.md` as sources of truth
  - Single comprehensive "Command Instructions" block (like /adr pattern)
  - One concise example conversation instead of three verbose ones
  - Eliminated: Tools section, Notes section, Implementation Details (redundant with pm/README.md)
  - Version: 0.2.0 → 1.0.0

- **Comprehensive reference updates**: All agents, templates, and documentation updated
  - Updated 19 agent files: HANDOFF.yml → WORKLOG.md references
  - Updated 24 template files: /architect → /adr references
  - Updated guideline references across commands
  - Updated pm/README.md with expanded epic patterns and WORKLOG/RESEARCH guidance
  - Documentation count corrections: 19 agents (was 20), 31 template files (was 30), 7 guidelines (was 6)

## [0.10.1] - 2025-10-22

### Added

- **CHANGELOG.md template**: Added to starter template (30 files total, was 29)
  - Template follows Keep a Changelog format
  - Initialized with project setup entry
  - Starter CLAUDE.md includes CHANGELOG maintenance instructions
  - AI agents instructed to remind about CHANGELOG updates after work completion

### Fixed

- **`/toolkit-init` copy completeness**: Updated documentation to emphasize ALL 30 files must be copied (was 29)
  - Added complete directory structure visualization (was incomplete)
  - Added verification steps to check file count after copy
  - Added critical warnings about hidden files (.gitignore) and .gitkeep files
  - Added multiple copy methods (cp, rsync, individual writes) with clear requirements
  - Issue: Command was missing nested directories (guidelines/, adrs/, design/)

## [0.10.0] - 2025-10-22

### NOTE

This represents a substantial rewrite to this project, with a heavy emphasis on simplifcation and streamlining to make it more of a base template than a prescriptive framework.

### Added

- **`/toolkit-init` Update/Sync Mode**: Intelligent template drift detection and selective updates
  - **Drift detection**: Compare project files vs latest plugin templates
  - **Interactive updates**: Choose keep/update/merge/diff/skip for each file
  - **Smart merge**: Automated section-based merging for markdown files
  - **Dry run mode**: Preview changes with `--dry-run` flag
  - **File status indicators**: ✅ Identical, 🔧 Customized, ❌ Missing, ➕ New in plugin
  - **Preserves customizations**: Never overwrites without user consent
  - **Use cases**: After plugin updates, periodic drift checks, selective file resets

- **`/comment` command**: Human-AI collaboration through timestamped work log entries
  - Add manual work notes to WORKLOG.md (e.g., `/comment "Added login button to header"`)
  - AI offers to update task plan based on human comments
  - Bidirectional communication channel between developers and AI agents
  - Accurate timestamps via `date` command (no date estimation)

- **`/branch` command**: Unified branching operations (create, merge, delete, switch, status)
  - Replaces `/merge-branch` with comprehensive branch management
  - Merge validation: Tests for `develop`, staging health checks for `main`
  - Natural language support: `/branch "merge to develop"`

- **Three-branch Git workflow**: Default branching strategy (main ← develop ← work branches)
  - Strict naming: `feature/TASK-###`, `bugfix/BUG-###`
  - Production safety: Only `develop` merges to `main` after staging validation

- **`ui-ux-designer` agent**: UI/UX design, accessibility, design systems (20 agents total)

- **`docs/design/` directory**: Structured storage for mockups, screenshots, color schemes, assets

- **`/implement` command**: Phase-based execution with test-first approach (replaces `/develop`)

- **`/docs` command**: Natural language documentation management (consolidates 5 doc commands)

- **ADR template and structure**: Standardized template at `docs/project/adrs/` with best practices guide

- **Architecture Overview template**: Technical specifications document at `docs/project/architecture-overview.md`

### Changed

- **BREAKING: WORKLOG.md replaces HANDOFF.yml** - Narrative work history instead of process metadata
  - **Old system (HANDOFF.yml)**: Tracked agent handoffs, deliverables, timestamps (process overhead)
  - **New system (WORKLOG.md)**: Narrative entries with lessons learned, gotchas, context (~500 char entries)
  - **Format**: `## YYYY-MM-DD HH:MM - agent-name` or `@username` for humans
  - **Entry order**: Reverse chronological (newest first for easy scanning)
  - **Accurate timestamps**: Uses `date '+%Y-%m-%d %H:%M'` command (no date estimation)
  - **Content**: Summary + Gotchas/Lessons + Files changed (helps AI remember context)
  - **Bidirectional**: Both AI agents (via `/implement`) and humans (via `/comment`) add entries
  - **Purpose**: Solves "AI forgets context" problem with implementation insights, not just metadata
  - **Migration**: HANDOFF.yml was never fully adopted, clean break for alpha/beta plugin
  - **Files**: TASK.md (WHAT), WORKLOG.md (HOW + lessons), RESEARCH.md (WHY)

- **BREAKING: Root CLAUDE.md** - Rewritten for plugin development (was plugin usage)
  - Focus: Version management, local testing, common dev tasks
  - Projects get CLAUDE.md from `templates/starter/` via `/toolkit-init`

- **`/toolkit-init` enhanced**: Now supports update/sync mode instead of exiting on re-init
  - **Old behavior**: Exit with "already initialized" message
  - **New behavior**: Compare templates, offer selective updates
  - **Flags added**: `--force` (overwrite all), `--dry-run` (preview only)

- **BREAKING: Command consolidation** - 21 → 14 commands (added `/comment`)
  - Consolidated: `/docs-*` (5 commands) → `/docs` (natural language)
  - Removed: `/review` (→ `/quality`), `/refresh` (→ `/status`), `/design` (redundant), `/improve` (obsolete)
  - Added: `/comment` for human work log entries

- **BREAKING: Scripts removed** - Deleted entire `scripts/` directory (44 files)
  - Pure AI-driven approach replaces bash/js script enforcement

- **`/toolkit-init`**: Ultra-minimal initialization (2 questions: name + description)

- **`/project-brief`**: Gap-driven conversation to complete brief

- **`/epic`**: Natural language conversation interface

- **BREAKING: `/adr` ADR location** - Single directory `docs/project/adrs/` (was split across multiple locations)
  - Migration: Move existing ADRs to `docs/project/adrs/` and renumber

- **Phase-based planning**: Test-first patterns in `/epic`, `/plan`, `/implement` (flexible, not required)

- **BREAKING: Guidelines as project configuration** - Minimal templates (6 files) copied to project (was 16 plugin files with fallback)
  - Location: `docs/development/guidelines/` in project
  - YAML frontmatter configures AI behavior per project

### Removed

- **BREAKING: `/merge-branch`** → `/branch merge` (part of unified `/branch` command)

- **BREAKING: `/develop`** → `/implement TASK-### PHASE` (phase-based execution)

- **BREAKING: `/docs-*`** → `/docs "natural language"` (5 commands consolidated)

- **Plugin hooks system** - Removed `hooks/` directory (referenced deleted scripts)

- **Plugin docs/guides/** - Deleted 1,645 lines (4 comprehensive guides)
  - Contradicted MVP philosophy, already outdated, duplicated Claude's knowledge
  - Added conceptual sections to AGENTS.md and COMMANDS.md instead

- **Plugin docs/examples/** - Removed development artifacts
  - Moved: architecture-template.md → starter template as architecture-overview.md

- **BREAKING: Flat PM structure** - Two directories: `pm/epics/`, `pm/issues/`
  - Epic files: `pm/epics/EPIC-###-name.md`
  - Issue dirs: `pm/issues/TASK-###-name/`, `pm/issues/BUG-###-name/`

- **BREAKING: `/plan` simplified** - Single-ID invocation only (removed `--epic`, `--task`, `--bug` flags)

- **BREAKING: Template-driven PM** - Customizable templates at `pm/templates/` (epic.md, task.md, bug.md)

- **BREAKING: Plugin guidelines** - Removed `docs/guidelines/` (16 files)
  - Guidelines now in project templates: `templates/starter/docs/development/guidelines/` (6 files)

- **BREAKING: Guideline fallback system** - Removed `guideline-mapping.yml`
  - Guidelines always at `docs/development/guidelines/` in project

## [0.9.1] - 2025-10-21

### Added

- **`/toolkit-init` command**: Initialize new or existing projects with minimal ai-toolkit structure
  - Interactive project customization with prompts for name, tech stack, and external links
  - Smart conflict resolution for existing files (skip, overwrite, or merge)
  - Includes GETTING-STARTED.md explaining AI-driven documentation approach
  - Works entirely offline with bundled templates - no git or network dependencies
  - Customizes CLAUDE.md with project-specific information during initialization
  - Supports `--force` flag to skip prompts and overwrite existing files
  - Supports `--dry-run` flag to preview changes without executing
  - Comprehensive summary report showing created/skipped/customized files

### Changed

- **BREAKING: Simplified Starter Template** - Reduced from 75 files (1MB) to 9 files (60K)
  - **Philosophy shift**: AI creates structure as you work, not upfront
  - **Removed**: 60+ placeholder/example files that users rarely needed
  - **Plugin docs moved**: AI Toolkit guides now in plugin (always up-to-date)
  - **Guidelines in plugin**: 16 development guidelines now in `plugins/ai-toolkit/docs/guidelines/`
  - **Examples in plugin**: ADR examples, architecture templates in `plugins/ai-toolkit/docs/examples/`
  - **Just-in-time docs**: Commands create docs/project/ structure when first needed
  - **Guideline fallback system**: Agents check project first, fall back to plugin defaults
  - **Project customization**: Copy guidelines from plugin only when project-specific rules needed
  - **Clean initialization**: Projects start minimal, grow organically through AI commands

- **Template Distribution**: Moved `starter-template/` to `plugins/ai-toolkit/templates/starter/`
  - Templates now bundled with plugin for offline initialization
  - Users no longer need to manually copy starter-template files
  - Template version stays locked to plugin version for consistency
  - 94% size reduction (1MB → 60K)

## [0.9.0] - 2025-10-19

### Changed

- **BREAKING: Marketplace + Plugin Architecture**: Complete restructuring from GitHub Template to Claude Code Plugin Marketplace
  - **Marketplace Structure**: Repository now serves as a Claude Code marketplace with embedded plugin
    - Created `.claude-plugin/marketplace.json` defining marketplace metadata
    - Plugin located at `plugins/ai-toolkit/` with full plugin structure
    - Starter template separated to `starter-template/` for project scaffolding
  - **Plugin Distribution**: AI Toolkit commands and agents now installable as plugin
    - 18 slash commands available as plugin (design, architect, plan, develop, quality tools, etc.)
    - 19 specialized agents included (brief-strategist, code-architect, frontend-specialist, ai-llm-expert, etc.)
    - All support scripts bundled in plugin using `${CLAUDE_PLUGIN_ROOT}` environment variable
  - **Plugin Renamed**: Changed from "ai-workflow" to "ai-toolkit" for better clarity
    - Updated all references in plugin.json, marketplace.json, and documentation
    - Plugin name better reflects comprehensive toolkit nature
  - **Local Development**: Marketplace structure enables easy local testing
    - Test locally with `/plugin marketplace add ./` from repository root
    - Immediate feedback loop for plugin development without publishing
    - Clean separation between plugin tooling and project scaffolding
  - **Bundled MCP Servers**: Plugin now auto-configures essential MCP servers
    - Bundled servers: context7 (library docs), sequential-thinking (problem solving), playwright (browser automation)
    - No manual MCP configuration required - tools ready immediately after plugin installation
    - Serena documented as optional addition for larger codebases (20+ files) in `docs/OPTIONAL-MCP-SERVERS.md`
    - MCP configuration in `plugins/ai-toolkit/.mcp.json` loads automatically
  - **Plugin Documentation Reorganization**: Cleaned up documentation structure
    - Moved `agents/README.md` → `docs/AGENTS.md` (24KB agent catalog)
    - Moved `commands/README.md` → `docs/COMMANDS.md` (10KB command reference)
    - Moved `agents/guideline-mapping.yml` → `docs/guideline-mapping.yml`
    - Created `docs/OPTIONAL-MCP-SERVERS.md` for Serena and other optional tools
    - Agent and command directories now contain only .md files for proper plugin loading
  - **Starter Template**: Pre-configured project structure separate from plugin
    - Template includes: CLAUDE.md, README.md, docs structure, .gitignore
    - Removed `.claude/cache/` directory (plugins installed globally, not cached per-project)
    - Users copy `starter-template/` to their project after installing plugin
    - Documentation moved to `starter-template/docs/` (project, development, ai-toolkit tiers)
  - **Script Updates**: All core scripts updated for plugin environment
    - Updated `scripts/lib/logging.sh` to use `${CLAUDE_PLUGIN_ROOT}` with fallback
    - Updated `scripts/status/ai-status.sh` to work as plugin or standalone
    - Hooks configuration uses plugin root for script paths
  - **Semantic Versioning**: Version bumped to 0.9.0 (major architecture change before 1.0.0)
  - **Rationale**:
    - Commands are technology-agnostic and reusable across all projects
    - Plugin distribution provides clean updates via `/plugin update ai-toolkit`
    - Marketplace enables local testing and development iteration
    - Separation of tooling (plugin) from scaffolding (template) improves clarity
  - **Result**: Users get cleaner projects, easier updates, better development workflow

### Removed

- **Old Directory Structures**: Cleaned up root directory from GitHub Template approach
  - Removed `.claude/` from root (now in `plugins/ai-toolkit/` for plugin)
  - Removed `example/` (example code not needed in marketplace)
  - Removed `src/` (example application not needed in marketplace)
  - Removed `workbench/` (development workspace not needed in marketplace)
  - Removed `.serena/` (tool-specific cache not needed in marketplace)

- **GitHub Template Distribution Files**: Removed obsolete template system files
  - Removed `.template-dev.json.example` (template development config)
  - Removed `.template-manifest.json` (GitHub template metadata)
  - Removed `.templateignore` (template file exclusions)

- **NPM Package Files**: Removed obsolete NPM distribution files
  - Removed `package.json` and `package-lock.json` (referenced deleted CLI scripts)
  - Archived `.archived-npm/` directory (old NPM package, now obsolete)

- **Development Git Hooks**: Removed development-only git configuration
  - Removed `.githooks/` directory (pre-commit hooks for this repo's development)
  - Removed `.githooks.json` (hooks configuration)
  - Removed `.gitmessage` (commit message template)

- **Miscellaneous Cleanup**: Removed unused configuration files
  - Removed `README.old.md` (backup file from conversion)
  - Removed `.env.example` (app-specific environment variables)
  - Removed root `.mcp.json` (MCP servers now bundled in plugin)
  - Removed `.claude/cache/` from starter-template (plugins installed globally)

## [0.8.2] - 2025-10-04

### Changed

- **Init Script Improvements**: Enhanced project initialization with cleaner output and better file handling
  - Removed license type prompt (LICENSE file is deleted anyway, users add their own)
  - STATUS.md now created fresh instead of sed replacements (removes all template development history)
  - Added .serena/ directory cleanup (template's code analysis cache shouldn't transfer to user projects)
  - Added CONTRIBUTING.md cleanup (template-specific contributing guidelines don't apply to user projects)
  - Result: Faster initialization, cleaner project files, no confusing template artifacts

- **Architect Command Enhancement**: Added explicit `--question` flag for direct questions
  - New flag: `--question "text"` for explicit Direct Question mode
  - Backward compatible: Quoted strings still work without flag
  - Eliminates quote-detection ambiguity for clearer user intent
  - Updated argument-hint to show all available flags and modes
  - Result: More predictable command parsing, better UX for quick architectural questions

- **Commit Command Enhancement**: Added git workflow flags for common operations
  - New flag: `--amend` - Amend last commit with safety checks (authorship, push status)
  - New flag: `--no-verify` - Skip pre-commit hooks for emergency fixes
  - New flag: `--interactive` - Interactive staging before commit
  - Safety features: Warns if amending other developer's commits or pushed commits
  - Result: Common git workflows accessible without remembering bash syntax

- **Test-Fix Command Enhancement**: Added test execution optimization flags
  - New flag: `--type TYPE` - Run only specific test type (unit/integration/e2e)
  - New flag: `--failed-only` - Re-run only previously failed tests
  - New flag: `--watch` - Watch mode for continuous testing on file changes
  - Flags combinable: `--type unit --failed-only` for fastest iteration
  - Result: Faster test iterations, developer productivity improvements

### Removed

- **Workbench STATUS.md**: Deleted outdated status file from NPM distribution phase
  - File was stale and duplicated root STATUS.md
  - Workbench directory is for active development only
  - Root STATUS.md remains as authoritative project status

## [0.8.1] - 2025-10-04

### Changed

- **Quick Start Documentation**: Added init-project script to all Quick Start options in README.md
  - All installation methods (GitHub Template, degit, manual clone) now clearly show the critical initialization step
  - Updated First Steps section to reflect what init-project creates automatically
  - Prevents users from skipping transformation step that customizes template for their project
  - Result: Users understand that init-project is required for ALL installation methods, not just degit

## [0.8.0] - 2025-10-04

### Fixed

- **Critical Setup Script Bug**: Fixed PROJECT_ROOT path calculation in setup-manager.sh
  - **Problem**: PROJECT_ROOT was resolving to `.claude/` instead of project root directory
  - **Root Cause**: Script moved from `scripts/setup/` to `.claude/resources/scripts/setup/` but path calculation not updated
  - **Impact**: LICENSE deletion, README customization, and git operations were using wrong base directory
  - **Solution**: Changed `PROJECT_ROOT="$(cd "$SCRIPTS_ROOT/../.." && pwd)"` to `PROJECT_ROOT="$(cd "$SCRIPTS_ROOT/../../.." && pwd)"`
  - **Result**: All init-project operations now correctly operate on project root

- **Documentation Path Accuracy**: Fixed incorrect script paths across multiple documentation files
  - **quick-start.md**: Updated 8+ script paths from `.resources/` to `.claude/resources/`
  - **integration-guide.md**: Fixed placeholder URLs and incorrect file references
  - **CONTRIBUTING.md**: Updated references to match new directory structure
  - **STATUS.md**: Refreshed next steps to reflect current project state
  - **Result**: All documentation paths now accurate after directory reorganization

- **README Documentation Accuracy**: Fixed multiple incorrect commands and references in project documentation
  - **Root README.md**: Fixed non-existent `npx ai-assisted-template setup` command references and updated GitHub URLs from placeholder "yourusername" to "TaylorHuston"
  - **Usage Guide**: Fixed incorrect script paths and GitHub URL placeholders in `docs/ai-toolkit/README.md`:
    - Updated GitHub URLs from "yourusername" to "TaylorHuston"
    - Replaced non-existent `npm run demo` with `npx ai-assisted-template status`
    - Fixed script paths: `setup-manager.sh` → `setup/setup-manager.sh`, `ai-status.sh` → `status/ai-status.sh`
  - **Result**: All documentation now contains only valid commands and accurate file paths

### Changed

- **Directory Structure Reorganization**: Moved `.resources/` to `.claude/resources/` for consistent .claude/ namespace
  - **Migration**: 254 files moved from `.resources/` to `.claude/resources/`
  - **Path Updates**: Updated all script paths, documentation references, and command configurations
  - **Structure**: All AI toolkit files now under single `.claude/` directory (agents, commands, resources, references)
  - **Benefits**: Cleaner namespace, consistent organization, easier .claude/ directory management
  - **Impact**: Updated 50+ file references across codebase to use new paths

- **README Consolidation**: Streamlined root documentation from two files to one
  - **Removed**: README-DEV.md (alpha-phase version with outdated content)
  - **Enhanced**: README.md with improved problem/solution framing
  - **Added**: "Why Intelligent Agent Coordination Matters" section to docs/ai-toolkit/README.md
  - **Result**: Single authoritative README with clear value proposition and accurate workflows

- **Documentation Structure Reorganization**: Improved documentation organization for better clarity and discoverability
  - **Directory Renaming**:
    - `docs/ai-tools/` → `docs/ai-toolkit/` - Better reflects the comprehensive AI development toolkit nature
    - `docs/technical/` → `docs/project/` - More intuitive naming for project-specific documentation
  - **Path Updates**: Updated all 50+ file references across codebase to use new directory structure
  - **Cross-Reference Integrity**: Maintained all internal links and cross-references during restructuring
  - **Package Distribution**: Updated NPM package configuration to include new documentation paths

- **Command Header Standardization**: Implemented consistent header format across all custom commands for improved maintainability
  - **Standardized YAML Frontmatter**: Unified structure with version, dates, status, audience, and model specifications
  - **Agent Coordination Sections**: Clear specification of primary, supporting, and quality gate agents for each command
  - **Consistent Titles**: Unified `/command-name Command` format across all 14 commands
  - **Usage Documentation**: Standardized argument hints, examples, and usage patterns

### Removed

- **LICENSE File from Template**: Removed template LICENSE during project initialization
  - **Rationale**: Template LICENSE was confusing - users thought it enforced license requirements on their projects
  - **Implementation**: init-project script now deletes LICENSE before git commit
  - **Manifest Update**: LICENSE added to "ignore" category (never syncs)
  - **Guidance**: Users should add their own LICENSE appropriate for their project
  - **Result**: Clean slate for users to choose their own license without confusion

### Added

- **CLAUDE.md Two-File System**: Preserves project customizations while allowing template improvements
  - **Template Reference**: `.claude/CLAUDE-template.md` syncs with template improvements
  - **Project Version**: Root `CLAUDE.md` never syncs, preserves customizations
  - **Helper Commands**: `claude-diff` and `claude-status` for comparing versions
  - **Tracking Header**: Clear guidance in root CLAUDE.md on template relationship
  - **Manifest Update**: Root CLAUDE.md moved to "user" category (preserve strategy)
  - **Result**: Users can customize CLAUDE.md while still receiving template improvements via manual merge

- **Template Development Sync System**: Bidirectional sync for active template development while building real projects
  - **Manifest-Driven**: Uses `.template-manifest.json` to identify template vs project files
  - **Sync Commands**: `pull`, `push`, `status`, `diff` for complete workflow
  - **Smart Detection**: Auto-detects template directory from common locations
  - **Conflict Handling**: Timestamp-based conflict detection with force override option
  - **Configuration**: `.template-dev.json` for user-specific settings (git-ignored)
  - **Documentation**: Comprehensive guide at `docs/ai-toolkit/setup/template-development.md`
  - **Use Case**: Discover improvements while building projects → sync to template → share across all projects
  - **Result**: Contributors can actively develop template based on real-world experience

- **GitHub Template Distribution**: Converted from NPM package to GitHub Template primary distribution
  - **One-Click Setup**: Users click "Use this template" → instant repository creation
  - **Git Isolation**: Each project gets clean git history, no template repo inheritance
  - **Simpler Workflow**: No NPM package management, no complex CLI tools
  - **Alternative: degit**: `npx degit TaylorHuston/ai-toolkit` for CLI users
  - **Archived NPM**: Moved CLI tools to `.archived-npm/` with detailed README
  - **Future Updater Tool**: NPM package repurposed for future template updater utility
  - **Result**: GitHub Template standard for scaffolds, simpler user experience

## [0.7.0] - 2025-09-20

### Added

- **Template-Maintainer Agent**: Automated template lifecycle management and self-improvement system
  - **Purpose**: Monitor user feedback, implement improvements, and automate publishing workflow
  - **Capabilities**: Feedback analysis, template enhancement, NPM publishing, adoption monitoring
  - **Command**: New `/improve` command with 4 modes (--feedback, --enhance, --publish, --monitor)
  - **Intelligence**: Deep understanding of template structure, file categorization, and user propagation
  - **Automation**: Complete publishing pipeline from user feedback to NPM distribution
  - **Result**: Template can now evolve itself based on real user patterns and needs

### Fixed

- **Interactive Project Brief Creation**: Fixed `/design --brief` command to conduct proper interactive discovery
  - **Problem**: Command was jumping straight to document generation instead of asking discovery questions
  - **Root Cause**: Agent system wasn't recognizing `brief-strategist` due to incorrect YAML frontmatter format
  - **Solution**:
    - Converted `brief-strategist.md` from markdown headers to proper YAML frontmatter format
    - Updated `design.md` command to explicitly mandate brief-strategist agent for `--brief` flag
    - Added 6-step interactive discovery process with structured questioning
  - **Result**: `/design --brief` now asks questions one at a time and only generates project brief after complete discovery

## [0.6.4] - 2025-09-19

### Fixed

- **NPM Binary Resolution**: Fixed npx command not working from within repository directories
  - **Problem**: `npx ai-assisted-template init` failed with "ai-template: not found" when run from repo subdirectories
  - **Solution**: Added `ai-assisted-template` binary name to package.json alongside existing `ai-template`
  - **Result**: Both commands now work everywhere: `npx ai-assisted-template init` and `npx -p ai-assisted-template ai-template init`

## [0.6.0] - [0.6.3] - 2025-09-19

Missing, issues with Claude Code prematurely publishing NPM versions and burning these version numbers.

## [0.5.4] - 2025-09-19

### Fixed

- **NPM Package Distribution**: Fixed `.mcp.json` and other configuration files missing from NPM package installations
  - **Root Cause**: Files were categorized correctly in template manifest but missing from package.json "files" field
  - **Solution**: Added `.mcp.json`, `.env.example`, `.githooks/`, and `.githooks.json` to NPM package "files" array
  - **Result**: MCP servers (context7, sequential-thinking, playwright, serena) now properly included in new projects
  - All configuration files now correctly distributed with NPM package

## [0.5.3] - 2025-09-19

### Fixed

- **MCP Configuration**: Fixed `.mcp.json` file and other configuration files missing from NPM package installations
  - **Root Cause**: Files were properly categorized in template manifest but missing from package.json "files" field
  - **Solution**: Added `.mcp.json`, `.env.example`, `.githooks/`, and `.githooks.json` to NPM package distribution
  - **Result**: MCP servers (context7, sequential-thinking, playwright, serena) now properly included in new projects
  - Template manifest categorization was already correct; issue was at NPM packaging layer

## [0.5.2] - 2025-09-19

### Fixed

- **Command Model Specifications**: Fixed incorrect model specifications in 11 Claude Code commands
  - Fixed 10 commands using `model: sonnet` to use `model: "claude-3-5-sonnet-20241022"`
  - Fixed 1 command using `model: opus` to use `model: "claude-opus-4-1"`
  - Resolved 404 errors preventing commands from executing (update-docs, refresh, review, etc.)
  - All custom commands now properly specify their required models with full model identifiers

### Removed

- **Template Cleanup**: Reduced template file count by 16.6% for cleaner installations
  - Removed 61 obsolete and duplicate files from template structure
  - Deleted deprecated feature workflow templates (replaced by epic-driven workflow)
  - Removed template's own development artifacts not needed by end users
  - Consolidated duplicate example files and testing patterns
  - Removed obsolete `.claude/working/` directory from previous workflow system
  - Deleted duplicate documentation files (MCP guide, collaborative workflow guide)
  - Reorganized `.claude/resources/` directory structure for better organization
  - Template now installs 306 files instead of 367 (exceeded target of ~290 files)

### Changed

- **Documentation Consolidation**: Streamlined documentation structure
  - Consolidated MCP documentation to single authoritative source in `docs/ai-toolkit/setup/mcp-setup.md`
  - Removed references to deprecated `/feature-development` workflow
  - Updated design command to explicitly reference correct template location
  - Updated all file path references in documentation to reflect reorganized structure
  - Refreshed `.claude/references/` documentation trees to maintain accuracy

### Added

- **MCP Configuration**: Fixed `.mcp.json` file categorization in template manifest
  - File now properly included in configuration category with smart-merge strategy
  - Ensures MCP server configurations are included in new projects
  - Enables out-of-the-box integration with context7, sequential-thinking, playwright, and serena MCP servers

## [0.5.1] - 2025-09-19

### Fixed

- **NPM Binary Execution Issue**: Fixed CLI binary not executable in published NPM package
  - Added execute permissions to `cli/index-npm.js` (changed from 644 to 755)
  - Resolved "ai-template: not found" error when using `npx ai-assisted-template` commands
  - NPM package now properly executes CLI commands: init, status, validate, info
  - Binary file now includes correct shebang and executable permissions for cross-platform compatibility
  - Confirmed working with `npx ai-assisted-template@0.5.1 init` command

## [0.5.0] - 2025-09-19

### Fixed

- **Critical NPM Package Installation Issue**: Fixed FileCategorizer baseDir handling for NPM package installations
  - Resolved issue where template installation was copying 0 files instead of expected 367 files
  - Fixed template path detection to correctly scan NPM package directory instead of user's working directory
  - Added baseDir parameter to FileCategorizer constructor and updated all file scanning methods
  - NPM package now successfully installs complete template with all 367 files (306 copied + 60 merged + 1 configured)
  - Confirmed working with `npx ai-assisted-template@0.4.1 init my-project` command
  - Package name changed from `@ai-template/core` to `ai-assisted-template` for simpler distribution

- **NPM Package Documentation**: Updated README.md with correct NPM package name and commands
  - Fixed installation command from `npx @ai-template/core` to `npx ai-assisted-template`
  - Updated status and validate commands to use NPM package format
  - Added README.md to NPM package files for proper documentation display
  - Removed circular dependency from package.json

## [0.4.0] - 2025-09-19

### Added

- **Template Distribution System**: Complete NPM package distribution with development sync capabilities
  - NPM package `@ai-template/core` for easy template installation via `npx @ai-template/core init`
  - CLI tools with commands: init, status, validate, dev enable/disable, sync pull/push
  - File categorization system with 6 categories (core, reference, optional, configuration, user, ignore)
  - Bidirectional development sync for template contributors working with live projects
  - Automatic git repository isolation for template installations to prevent inheritance issues
  - Example directory structure with working web-app template installation (370+ files)
  - Template manifest system (.template-manifest.json) for intelligent file handling
  - Successfully tested: template installation, development mode sync (both push/pull directions)

- **Comprehensive Metrics Collection System**: Advanced analytics for commands, agents, and scripts
  - Unified metrics schema tracking execution patterns, performance, and dependencies
  - JSONL-based storage with configurable retention and privacy controls
  - Real-time collection hooks for commands, agents, and script executions
  - Analytics tools: report generation, query interface, and statistics dashboard
  - Integration-ready with existing CI/CD, pre-commit hooks, and automation workflows
  - Actionable insights for system optimization and usage pattern analysis

- **Terminology Standardization**: Unified project planning terminology from "vision" to "project-brief"
  - Updated `/design --vision` to `/design --brief` for improved clarity and consistency
  - Renamed vision-strategist agent to brief-strategist agent
  - Updated all template files: vision.template.md → project-brief.template.md
  - Standardized all documentation references to use "project-brief" terminology
  - Updated workflow scripts and validation tools to use consistent naming

- **Documentation System Optimization**: Comprehensive optimization of development guidelines and documentation structure
  - Consolidated 19 guideline files down to 12 files (37% reduction) for improved AI processing efficiency
  - Extracted code examples from guidelines to `.claude/resources/examples/` directory with organized subdirectories
  - Streamlined git-workflow.md from 913 to 308 lines (66% reduction) focusing on project-specific workflows
  - Added MCP Tool Decision Framework to CLAUDE.md to prevent manual analysis when systematic tools are available
  - Updated all cross-references and agent guideline mappings to reflect consolidated structure
  - All guideline files now under 400 lines for optimal AI context usage

### Changed

- **Refresh Command Performance**: Optimized `/refresh` command for dramatically improved context efficiency
  - Reduced context consumption from 58k tokens to ~300 tokens (194x improvement)
  - Changed from direct file reading to subagent delegation pattern using context-analyzer
  - Moved static capability information to CLAUDE.md to avoid redundant loading
  - Implemented just-in-time guideline loading system to prevent context waste
  - Maintained complete functionality while achieving massive efficiency gains

- **Plan Command Enhancement**: Restructured `/plan` command for interactive planning and workbench organization
  - Added interactive standalone task support with `/plan --misc "task name"` and `/plan --bug "description"`
  - Implemented auto-incrementing ID system (MISC-### and BUG-###) based on existing task numbers
  - Added conversational requirements gathering to eliminate ambiguity through back-and-forth dialogue
  - Created workbench structure: `workbench/epics/`, `workbench/misc/`, and `workbench/bugs/`
  - Enhanced task template generation with kebab-case directory formatting and comprehensive HANDOFF.yml

- **Development Command Enhancements**: Enhanced `/develop` command with enforced coordination and teaching capabilities
  - **HANDOFF.yml Enforcement**: Mandatory agent coordination tracking with 5 enforcement checkpoints
  - **Structured handoff chain**: Real-time agent transitions with timestamps and deliverable documentation
  - **Validation requirements**: Complete handoff verification before task completion
  - **Restored `--guided` flag**: Teaching mode with code suggestions instead of direct modifications
  - **Enhanced user control**: Learning-focused development with explanation and review cycles

## [0.3.0] - 2025-09-18

### Added in v0.3.0

- **Epic-Driven Workflow**: New epic-driven development workflow with progressive task discovery
  - Epic structure: `epics/[name]/EPIC.md` with task directories and `resources/` for reference materials
  - Progressive task discovery across all workflow phases with automatic task numbering
  - X.Y.Z implementation task numbering for precise progress tracking (e.g., TASK-001:1.2.3)
  - Task directory structure with TASK.md, HANDOFF.yml, and RESEARCH.md for comprehensive context

- **Comprehensive Testing Integration**: Hybrid TDD/BDD testing strategy with extensive coverage requirements built into the commands and workflow
  - 95%+ test coverage target enforced across all development phases
  - BDD test scenarios generated from acceptance criteria in `/design` phase
  - Testing architecture decisions documented in `/adr` phase ADRs
  - Dedicated testing tasks created during `/plan` phase
  - Test-first development enforced in `/develop` phase with auto-invoked test-engineer agent
  - New testing-specific templates for comprehensive test planning and execution

- **Epic-Driven Hierarchical Branching Strategy**: Git workflow optimized for epic development structure
  - **Epic branches** (`epic/[name]`) contain multiple task branches (`task/###-[name]`)
  - **Progressive task discovery**: Tasks numbered by discovery order across workflow phases
  - **Local merge workflow**: Integration with existing `/merge-branch` command for quality gates
  - **Epic branch manager script**: Automated branch creation, merge setup, and cleanup utilities

- **Enhanced Command Integration**: All commands updated for epic workflow
  - `/design` creates epic structures, task directories, and BDD test scenarios
  - `/adr` uses Quick/Deep modes with optimized ADR generation and agent coordination
  - `/plan` adds X.Y.Z implementation details, agent coordination, and dedicated testing tasks
  - `/develop` executes with full epic context, dependency management, and test-first enforcement

### Changed

- Templates updated to support epic workflow and task directories
- Reorganized and consolidated templates, scripts and examples into .claude/resources directory
- Standardized template name formats

### Removed

- **Deprecated Commands**: Streamlined command system by removing redundant and obsolete commands
  - `/feature-development` - Replaced by epic-driven `/design` → `/adr` → `/plan` → `/develop` workflow
  - `/health-check` - Functionality consolidated into `/quality assess` command
  - `/progress` - Progress tracking integrated into `/status --detailed` command
  - **Command reduction**: 17 → 14 commands (18% reduction) with improved clarity and no functionality loss

### Fixed

- **Claude Code Compliance**: Updated all custom commands to meet Claude Code documentation standards
  - Added required frontmatter fields: `description`, `allowed-tools`, `argument-hint`, `model`
  - Standardized `allowed-tools` formatting to consistent array syntax with quotes
  - Proper model assignment based on command complexity (opus for complex, sonnet for standard)
  - All 14 remaining commands now fully compliant with Claude Code requirements

## [0.2.0] - 2025-09-18

### Added in v0.2.0

- Example app vision document for multi-user todo application
- Template validation project initiated through /vision workflow
- Complete vision framework with success metrics and feature definitions

### Fixed in v0.2.0

### Changed in v0.2.0

- **MAJOR**: Consolidated `docs-sync-agent` into `technical-writer` agent for streamlined documentation workflow
  - Single comprehensive documentation agent handling creation, maintenance, and synchronization
  - Eliminates decision paralysis between overlapping documentation agents
  - Maintains all existing functionality while improving user experience
  - Updated all 31+ references across commands, hooks, and documentation

- **Command Simplification**: Reduced AI workflow command instructions by 77% (2,470 → 575 lines)
  - Eliminated scripted conversation patterns that constrained AI behavior
  - Improved performance by reducing 15-20% AI overhead from excessive instructions
  - Maintained essential objectives while trusting AI's natural capabilities

- Initiated example application development to validate template workflows

## [0.1.0] - 2025-09-18

### Added in v0.1.0

- Initial release version after much random prototyping and experimentation
