# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.31.0] - 2025-11-18

### Removed

- **`/refresh-schema` command** - Field cache now auto-refreshes automatically
  - Jira field cache refreshes on discovery failures
  - No manual cache management needed
  - Reduces command count from 26 to 25

### Changed

- **Jira command naming** - Renamed 4 commands with `jira-` prefix for clarity
  - `/epic` → `/jira-epic`
  - `/import-issue` → `/jira-import`
  - `/promote` → `/jira-promote`
  - `/comment-issue` → `/jira-comment`
  - **Why**: Makes Jira requirement immediately clear
  - **Breaking**: Users must update to new command names

- **Command bloat reduction** - Refactored 6 commands following "commands orchestrate, guidelines configure" principle
  - Reduced 6 commands from 1,815 → 662 lines (64% reduction)
  - Moved implementation details to referenced guidelines
  - Kept only high-level orchestration flow
  - Commands: `/implement`, `/plan`, `/jira-epic`, `/spec`, `/jira-promote`, `/advise`
  - **Goal**: Faster loading, easier maintenance, clearer purpose

- **WORKLOG review entry requirement** - Made mandatory review agent entries explicit
  - **worklog-format.md**: Added MANDATORY markers for code-reviewer and security-auditor entries
  - **development-loop.md "Code Review Process"**: Emphasized code-reviewer MUST write separate WORKLOG entry
  - **development-loop.md "Per-Phase Gates"**: Added quality gates #4 and #7 for review WORKLOG entries
  - **Phase loop updated**: code-reviewer writes entry BEFORE commit, implementation agent writes after
  - **Why**: Creates audit trail, provides detailed feedback for future agents, documents quality decisions
  - **What changed**: Implementation agents should NOT write review results - reviewers document their own findings
  - **Goal**: Ensure every phase has detailed review documentation in WORKLOG, not just score mentions

- **Guideline reorganization** - Reorganized docs/development/guidelines/ into 4 focused directories
  - **New structure**:
    - `conventions/` (7 files) - Project conventions and rules for both AI and humans
    - `workflows/` (9 files) - Concrete step-by-step AI execution protocols
    - `misc/` (4 files) - Command/agent reference docs, integration guides, and MCP server documentation
    - `templates/` (12 files) - All PM templates + documentation templates
  - **Template consolidation**:
    - Moved PM templates from `pm/templates/` to `docs/development/templates/`
    - Moved project-brief template from `docs/project-brief.md` to `docs/development/templates/project-brief.md`
    - Moved ADR template from `docs/project/adrs/adr-template.md` to `docs/development/templates/adr-template.md`
    - Moved and renamed architecture overview: `docs/project/architecture-overview.md` → `docs/development/templates/architecture-overview-template.md`
    - Moved and renamed design overview: `docs/project/design-overview.md` → `docs/development/templates/design-overview-template.md`
    - Moved and renamed writing style: `docs/project/writing-style.md` → `docs/development/templates/writing-style-template.md`
    - Project brief file still lives at `docs/project-brief.md` (created from template by `/toolkit-init`)
    - ADR instances still live in `docs/project/adrs/` (created from template by `/adr`)
    - Architecture overview instance still lives at `docs/project/architecture-overview.md` (created from template by `/toolkit-init`)
    - Design overview instance still lives at `docs/project/design-overview.md` (created from template by `/toolkit-init`)
    - Writing style instance still lives at `docs/project/writing-style.md` (created from template by `/toolkit-init`)
  - **Reference docs moved to user projects**:
    - Moved `commands.md` from `plugins/ai-toolkit/docs/` to `docs/development/misc/`
    - Moved `agents.md` from `plugins/ai-toolkit/docs/` to `docs/development/misc/`
    - Moved `optional-mcp-servers.md` from `plugins/ai-toolkit/docs/` to `docs/development/misc/`
    - Users can now reference command/agent docs and discover optional MCP servers directly in their projects
  - **File splits** - Split 4 oversized files to keep all under 500 lines:
    - `worklog-format.md` (797) → `worklog-format.md` (339) + `worklog-examples.md` (420)
    - `pm-guide.md` (706) → `pm-file-formats.md` (308) + `pm-workflows.md` (579)
    - `development-loop.md` (833) → `development-loop.md` (623) + `quality-gates.md` (333)
    - `agent-coordination.md` (550) → kept as-is (only 50 lines over limit)
  - **Path updates**: Updated all 25 command files with new guideline paths
  - **Why**: Clearer purpose separation, easier navigation, manageable file sizes, all templates in one location
  - **Breaking**: Guideline paths changed (automated in /toolkit-init)

### Fixed

- **`/sync-progress` missing YAML frontmatter** - Added complete frontmatter metadata
  - Command now properly discoverable in `/help`
  - Metadata: tags, description, allowed-tools, references

## [0.30.0] - 2025-11-17

### Added

- **`/implement --task` flag** - Automated full-task execution until completion or blocked
  - **Usage**: `/implement --task TASK-###` or `/implement --task` (auto-detect)
  - **Behavior**: Executes all remaining phases in sequence following mandatory test-first loop
  - **Continues until**: All phases complete, error occurs, or user input needed
  - **Per-phase**: Tests (red) → Code (green) → Review (≥90) → Commit → WORKLOG → Next phase
  - **Progress tracking**: Updates PLAN.md and WORKLOG.md after each phase completion
  - **Blocking scenarios**: Test failures, code review below threshold, user decisions needed
  - **Example**: Complete 4-phase task automatically with quality gates enforced at each step
  - **Goal**: Enable hands-off execution of well-defined tasks while maintaining quality standards

## [0.29.0] - 2025-11-17

### Changed

- **Command rename: `/comment` → `/worklog`** - Renamed for clarity about what the command does
  - Command writes WORKLOG entries, so `/worklog` is more descriptive than `/comment`
  - Updated all references across documentation and templates
  - **Breaking**: Users must use `/worklog` instead of `/comment` (functionality unchanged)

## [0.28.0] - 2025-11-17

### Changed

- **Mandatory test-first phase loop** - Enforced strict test-first loop for all phase execution
  - **pm-guide.md "Test-First Phase Loop"**: Replaced flexible "Alternative Patterns" with mandatory enforcement of tests → code → review → commit → worklog → next phase
  - **Quality gates (ALL required)**: Tests written and initially failing (red), tests passing after implementation (green), code review score ≥90
  - **`/implement` command**: Updated to enforce mandatory loop with quality gate validation before phase completion
  - **plan.md template**: Added "Mandatory Phase Execution" section referencing pm-guide.md loop protocol
  - **Progress Tracking Protocol**: Updated to require all quality gates pass before proceeding to next phase
  - **Example loop**: Phase 1.1 → Write tests (red) → Write code (green) → Code review (≥90) → Commit → WORKLOG → Phase 1.2
  - **Goal**: Ensure robust code quality, prevent regressions, maintain clean git history with tested incremental commits
  - **No shortcuts**: Tests cannot be skipped, code review cannot be skipped, phases cannot be combined

## [0.27.0] - 2025-11-17

### Changed

- **Plan philosophy: Strategic vs Tactical** - Clarified that PLAN.md describes WHAT to build, not HOW
  - **Strategic plans**: Describe objectives and outcomes (what to achieve)
  - **Tactical implementation**: Specialist agents decide HOW based on current codebase state and WORKLOG
  - **plan.md template**: Added "Implementation Philosophy" section emphasizing strategic approach and living document nature
  - **pm-guide.md "Agile Plan Updates"**: Enhanced with strategic/tactical distinction, WORKLOG integration, and examples
  - **`/plan` command**: Added guidance to generate strategic phases, not tactical prescriptive steps
  - **`/implement` command**: Emphasized agents read WORKLOG for context and decide implementation approach autonomously
  - **Example**: ✅ "Implement user model with password hashing" vs ❌ "Create User class with bcrypt.hash() using 10 salt rounds"
  - **Goal**: Prevent outdated prescriptive plans; let specialist agents adapt to current code and lessons learned

- **PM workflow scoping clarity** - Emphasized task/phase scoping principles across documentation and templates
  - **Task scope = Deployable change**: Tasks should be independently deployable to main (1-3 days, 3-8 phases)
  - **Phase scope = Commit point**: Phases are testable, reviewable, committable steps within tasks (1-4 hours)
  - **pm-guide.md**: Added "Task and Phase Scoping Principles" section with examples of properly/improperly scoped work
  - **`/plan` command**: Added scoping validation step to warn if task appears too small to deploy independently
  - **task.md template**: Added scoping hint emphasizing deployable changes
  - **spec.md template**: Updated task breakdown guidance to emphasize independently deployable tasks
  - **Goal**: Prevent overly granular tasks that should be phases within larger deployable work

## [0.26.0] - 2025-11-17

### Added

- **`/sync-progress` command** - Automatically sync project state after manual changes
  - Analyzes git diff to understand what changed
  - Updates PLAN.md to reflect completed work or new direction
  - Documents changes in WORKLOG with inferred intent
  - Suggests next steps based on remaining plan
  - **Use case**: Made manual code changes offline and want AI to catch up without manual explanation
  - **Command count**: 25 → 26 commands

### Fixed

- **Command count accuracy** - Corrected command count from 24 to 26 across all documentation (was showing 24, actual count was 25 before sync-progress)

## [0.25.0] - 2025-11-17

### Added

- **Cloud platform expert agents** - three new specialized agents for cloud architecture guidance
  - **aws-expert** - AWS Solutions Architect specialist for AWS service selection, architecture design, cost optimization, and security best practices following AWS Well-Architected Framework
  - **azure-expert** - Azure Solutions Architect specialist for Azure services, Microsoft ecosystem integration, hybrid cloud scenarios, and Azure Well-Architected Framework
  - **gcp-expert** - Google Cloud Solutions Architect specialist for GCP services, data analytics (BigQuery), AI/ML (Vertex AI), Kubernetes (GKE), and Google Cloud Architecture Framework
  - **Use cases**: Multi-cloud comparison, platform-specific implementation guidance, cost optimization, architecture design, migration planning
  - **Model**: claude-opus-4-1 (all three) for critical architectural decisions
  - **Agent count**: 18 → 21 agents

### Changed

- **Agent standardization** - aligned all 18 agents with agent-template.md format
  - **Added**: Universal Rules section to all agents (CLAUDE.md + WORKLOG protocols)
  - **Fixed**: context-analyzer YAML frontmatter (`invoked_by` → `receives_from`)
  - **Reduced**: Condensed 3 agents to meet 350-line limit
    - context-analyzer: 517 → 326 lines (condensed verbose examples and research strategies)
    - migration-specialist: 382 → 342 lines (condensed YAML catalogs in workflow)
    - ui-ux-designer: 371 → 342 lines (condensed design methodology catalogs)
  - **Benefits**: Consistent structure, clearer guidelines, reduced verbosity, better Context7 integration references

## [0.24.0] - 2025-11-13

### Added

- **spec.md template enhancement** (v2.1.0 → v2.2.0) - agent-optimized specifications
  - **Added**: "Users & Actors" section (required) - Who uses this feature and their roles
    - **AI agent value**: security-auditor identifies privilege levels, test-engineer creates role-specific tests, architects design access controls
    - **Format**: Brief list with role descriptions
    - **Placement**: After Description, before Acceptance Scenarios
  - **Added**: "Out of Scope" section (optional) - Explicitly excluded features
    - **AI agent value**: Prevents over-engineering, focuses implementation effort, clarifies boundaries
    - **Format**: List of excluded features
    - **Placement**: After Acceptance Scenarios, before Definition of Done
  - **Added**: "Success Metrics" section (optional) - Measurable quality targets
    - **AI agent value**: test-engineer sets thresholds, performance-optimizer targets, security validation
    - **Format**: Concrete measurable criteria (coverage %, response time, security requirements)
    - **Placement**: After Definition of Done, before Dependencies
  - **Enhanced hints**: Acceptance Scenarios now reference actors, improved examples throughout
  - **Backward compatible**: All new sections optional except Users & Actors

### Changed

- **BDD acceptance scenarios integration** - testable, outcome-driven specifications
  - **spec.md template** (v2.0.0 → v2.1.0): Added "Acceptance Scenarios" section with Given-When-Then format
  - **Location**: Between Description and Definition of Done
  - **Format**: 2-5 scenarios per spec (happy paths and critical edge cases)
  - **Guidance**: "These scenarios become test phases" hint in template
  - **Example**: "Given user has valid credentials, When submits login form, Then redirected to dashboard"

- **`/spec` command enhancement** - now prompts for acceptance scenarios
  - **Creation flow**: Explicitly asks for 2-5 Given-When-Then scenarios
  - **Inline examples**: Shows format during conversation
  - **Test-first alignment**: Emphasizes scenarios become test phases in `/plan`
  - **Updated output**: Example shows 3 acceptance scenarios in conversational format

- **`/plan` command enhancement** - scenario-driven test phase generation
  - **Step 1**: Now reads parent SPEC.md and extracts acceptance scenarios (if TASK references spec)
  - **Step 2**: Deep thinking considers how to validate each scenario
  - **Step 6**: Generates test phases covering scenarios with explicit traceability
  - **Step 8**: Presents "Scenario Coverage" mapping showing which phases validate which scenarios
  - **Example output**: Shows scenario → phase mappings and coverage confirmation
  - **Flexibility**: Works with or without parent spec (falls back to TASK.md acceptance criteria)

- **plan-structure.md guideline** - scenario-driven planning pattern documented
  - **New section**: "Scenario-Driven Planning" with when/how guidance
  - **Example**: Complete workflow from SPEC scenarios → PLAN phases → tests
  - **Traceability**: Shows explicit mapping between scenarios and test phases
  - **Benefits**: Direct spec → plan traceability, coverage verification, reduced ambiguity

- **issue-management.md guideline** - SPEC.md format updated
  - **Added section**: "Acceptance Scenarios" with 3 detailed Given-When-Then examples
  - **Scenarios**: Login success (happy path), invalid password (error), inactive account (edge case)
  - **Guidance**: "These scenarios become test phases in /plan"

- **plan.md template** (added "Scenario Coverage" section)
  - **Section added**: Between Phases and Alternative Patterns
  - **Purpose**: Maps acceptance scenarios to test phases
  - **Required**: false (only when TASK references parent SPEC)
  - **Format**: structured-list showing traceability

- **Guideline consolidation** - reduced from 3 files (1,678 lines) to 2 files (1,044 lines)
  - **BREAKING**: Deleted issue-management.md, pm-guidelines.md, plan-structure.md
  - **Created**: pm-guide.md (605 lines) - core PM workflows and plan execution
  - **Created**: jira-integration.md (439 lines) - Jira-specific integration patterns
  - **Rationale**: Templates are source of truth for structure; guidelines for workflows only
  - **Benefits**: Clearer separation (core vs integrations), easier maintenance, Jira-agnostic by default
  - **Updated**: 12 command references, 6 template references, 3 guideline cross-references

**Impact**: Complete spec-driven workflow with traceability from feature scenarios → implementation plans → test code. Enhanced agent planning with actor context, scope boundaries, and quality targets.

## [0.23.0] - 2025-11-07

### Breaking Changes

- **Epic → Feature Spec terminology pivot** - aligned with spec-driven development trends
  - **Rationale**: "Epic" overloaded (Jira vs local), "Feature Spec" clearer and trendy (spec-driven development movement)
  - **Local changes**: EPIC-### → SPEC-###, pm/epics/ → pm/specs/, pm/templates/epic.md → spec.md
  - **Commands split**: `/spec` (local specs) vs `/epic` (Jira-only)
  - **Migration**: Existing projects using pm/epics/ must rename to pm/specs/ and update issue references

### Added

- **`/spec` command** - create and manage local feature specifications
  - **Usage**: `/spec` (new), `/spec SPEC-###` (existing), `/spec --epic PROJ-###` (from Jira)
  - **Purpose**: Document WHAT to build (local files, version controlled)
  - **Location**: `pm/specs/SPEC-###-name.md`
  - **Separation**: Local specs (SPEC-###) vs Jira epics (PROJ-###)
  - **Workflow patterns**: Spec-first (recommended), epic-first, spec-only

### Changed

- **`/epic` command** - now Jira-only (requires integration enabled)
  - **Breaking**: No longer handles local mode (use `/spec` instead)
  - **Usage**: `/epic` (new Jira epic), `/epic PROJ-###` (existing), `/epic --spec SPEC-###` (from local spec)
  - **Requirement**: Jira integration must be enabled in CLAUDE.md
  - **Error handling**: Clear messaging when Jira not configured
  - **Semantic separation**: Epic = PM tracking container (Jira), Spec = specification (local)

- **Starter template structure** - updated for spec terminology
  - **Directory renamed**: pm/epics/ → pm/specs/
  - **Template renamed**: pm/templates/epic.md → spec.md
  - **Template updated**: Epic → Feature Spec (version 2.0.0)
  - **Guidelines updated**: All references to EPIC-### → SPEC-###
  - **Files affected**: issue-management.md, pm-guidelines.md, development-loop.md, plan-structure.md, pm/README.md, docs/development/README.md, CLAUDE.md, GETTING-STARTED.md

- **Keywords updated** - reflects spec-driven development focus
  - **Removed**: "epic-management"
  - **Added**: "spec-driven-development", "feature-specs"
  - **Files**: plugin.json, marketplace.json

- **Command count** - increased from 23 to 24 commands (added `/spec`)
  - **Updated**: README.md, plugin.json description, marketplace.json description

### Migration Guide

**For existing projects using pm/epics/:**

```bash
# 1. Rename directory
mv pm/epics pm/specs

# 2. Rename template
mv pm/templates/epic.md pm/templates/spec.md

# 3. Update all EPIC-### references to SPEC-###
find pm/ -type f -name "*.md" -exec sed -i 's/EPIC-/SPEC-/g' {} +

# 4. Update epic references in guidelines
find docs/development/guidelines/ -type f -name "*.md" -exec sed -i 's/epic/feature spec/g; s/pm\/epics/pm\/specs/g' {} +
```

**For Jira users:**
- Continue using `/epic` for Jira epic management (requires integration)
- Use `/spec` for local specifications
- Create bidirectional workflow: `/spec --epic PROJ-###` or `/epic --spec SPEC-001`

## [0.22.0] - 2025-11-07

### Added

- **Jira integration documentation** - dedicated guide for Jira integration setup and usage
  - **Location**: `plugins/ai-toolkit/docs/jira-integration.md`
  - **Content**: Requirements, setup, workflows, commands, limitations, troubleshooting
  - **Commands documented**: `/import-issue`, `/promote`, `/comment-issue`, `/refresh-schema`
  - **README refactor**: Replaced ~100 lines with brief overview linking to detailed guide
  - **Benefits**: Clearer documentation structure, room for future integrations (Linear, Notion, etc.)

### Changed

- **Documentation standardization** - unified naming conventions and removed versioning metadata
  - **File naming**: All docs now use lowercase-kebab-case (AGENTS.md → agents.md, COMMANDS.md → commands.md, OPTIONAL-MCP-SERVERS.md → optional-mcp-servers.md)
  - **Consistency rule**: Only README and CLAUDE in root use uppercase, all other files lowercase-kebab-case
  - **Versioning removed**: Eliminated version/created/last_updated from doc frontmatter (guidelines pattern)
  - **Broken links fixed**: 18 broken agent references in agents.md updated from `./` to `../agents/`
  - **CLAUDE_PLUGIN_ROOT clarified**: agent-template.md now documents valid usage (commands) vs invalid (agents)
  - **Metadata cleanup**: Streamlined frontmatter to target_audience, document_type, priority, tags only

## [0.21.0] - 2025-11-06

### Added

- **Development notes system** - atomic note files for parking lot captures
  - **Location**: `pm/notes/` directory with timestamped atomic note files
  - **Template**: `pm/templates/note.md` with frontmatter (type, context, impact, created)
  - **Naming convention**: `YYYY-MM-DD-HHMMSS-slug.md` for chronological sorting
  - **Workflow**: Conversational capture (agent asks, user asks), manual review/promote/discard
  - **Use cases**: Minor bugs, performance ideas, tech debt, feature ideas discovered during work
  - **Promotion**: `mv` note to TASK-###.md or BUG-###.md when ready to act on it
  - **Documentation**: issue-management.md "Development Notes" section with full workflow
  - **Benefits**: Zero overhead, atomic files, git-friendly, flexible via conversation
  - **Template count**: Increased from 37 → 39 files (added pm/notes/.gitkeep and pm/templates/note.md)

### Changed

- **Phase granularity guidance** - strengthened principles for atomic, testable, committable phases
  - **New section in plan-structure.md**: "Phase Granularity - Atomic, Testable, Committable"
  - **Core principle**: Each phase should end at a natural commit point (stable, working state)
  - **Good characteristics**: Atomic (one concept), testable (can verify), committable (rollback point), scoped (1-4 hours)
  - **Examples**: Good vs bad phase granularity with clear patterns
  - **Guidance**: When to split phases (>4 hours, multiple concepts) and when to merge (trivial, interdependent)
  - **Integration**: Links to phase commit tracking workflow
  - **Benefits**: Meaningful rollback points, clear progress checkpoints, better agent handoffs

## [0.20.0] - 2025-11-06

### Changed

- **Guidelines cleanup and compression** - reduced verbosity while preserving essential information
  - **Total reduction**: ~520 lines across 11 guideline files (from 6,396 → ~5,876 lines)
  - **Phase 1 - Single source of truth fixes**: Removed duplicated content with clean references
    - plan-structure.md: Security auto-detection, WORKLOG philosophy, test coverage (saved ~34 lines)
    - git-workflow.md: Quality gates duplication (saved ~7 lines)
  - **Phase 2 - Verbose examples trimmed**: Compressed detailed examples to essential patterns
    - coding-standards.md: Getting Started checklist 40 → 17 lines (saved 23 lines)
    - git-workflow.md: Branch lifecycle 94 → 34 lines (saved 60 lines)
    - development-loop.md: Workflow examples 73 → 22 lines (saved 51 lines)
    - versioning-and-releases.md: Release workflows 67 → 22 lines (saved 45 lines)
  - **Phase 3 - Optional guidelines TBD compression**: Removed placeholder bloat, leveraged YAML frontmatter
    - api-guidelines.md: 113 → 40 lines (saved 73 lines)
    - architectural-principles.md: 173 → 79 lines (saved 94 lines)
    - security-guidelines.md: 178 → 75 lines (saved 103 lines)
  - **Phase 4 - Design tokens trimmed**: Pattern demonstrations instead of exhaustive listings
    - ui-design-guidelines.md: Color/typography/spacing tokens (saved ~41 lines)
  - **Phase 5 - Cross-references validated**: All file references and links verified working
  - **Principle maintained**: Everything AI needs to work effectively, nothing more

### Added

- **Agile workflow for TASK/PLAN/WORKLOG files** - formalized inspect-and-adapt cycle
  - **Philosophy**: TASK.md and PLAN.md are living documents that evolve based on implementation learnings
  - **Review cycle**: After each phase → Run reviews (code/security/architecture) → Adapt plans → Document changes → Proceed
  - **New section in plan-structure.md**: "Agile Plan Updates" with when/how to update cleanly
  - **New section in development-loop.md**: "Review and Adapt Plans" with 5-step adaptation process
  - **New WORKLOG format**: "PLAN CHANGES" entry for documenting adaptations (worklog-format.md)
  - **Command integration**: /implement now includes post-phase review cycle (step 7) before phase completion
  - **Living documents guidance**: issue-management.md updated with file lifecycle and size expectations
  - **Size expectations**: TASK 50-200 lines, PLAN 300-600 lines, WORKLOG 400-800 lines (~1,200 total for complex tasks)
  - **Key principle**: Update files cleanly with current state, don't preserve debate history - WORKLOG records why changes were made
  - **Benefits**: Plans adapt to reality, requirements discovered incrementally, files stay manageable, audit trail preserved

- **Documentation synchronization quality gate** - validates project docs before merge to develop
  - **New Per-Task Gate #6**: Project documentation synchronized (development-loop.md)
  - **Comprehensive checklist**: Validates architecture-overview.md, design-overview.md, README.md, CLAUDE.md, guidelines
  - **Integration points**: git-workflow.md merge validation, plan-structure.md completion checklist
  - **Purpose**: Prevents documentation drift as features evolve
  - **Smart validation**: Checklist format with file-specific criteria (components, tech stack, features, workflows, patterns)
  - **Note included**: "Not every task requires updates to all files - use judgment"
  - **Benefits**: Documentation stays current, architecture decisions captured, design changes tracked, workflow improvements documented

- **Phase commit tracking** - atomic rollback points for each completed phase
  - **New WORKLOG section**: "Phase Commits" summary at top of WORKLOG.md with commit IDs
  - **Workflow**: Complete phase → Commit → Get commit ID → Add to Phase Commits section → Commit reference
  - **Format**: `- Phase X.Y: \`commit-id\` - Brief description` (one line per phase)
  - **Integration**: New step #5 in plan-structure.md "After Each Phase Completion" protocol
  - **Documentation**: worklog-format.md includes Phase Commits Summary section format and rollback example
  - **Benefits**: Visual rollback map, easy git reset to any phase, no chicken-and-egg problem with commit IDs
  - **Use case**: `git reset --hard abc123d` to roll back to specific phase completion state

- **Resources section in CLAUDE.md template** for curated link tracking
  - **Purpose**: Living documentation of valuable external resources discovered during development
  - **Categories**: Documentation, Community Resources, Example Implementations, Similar Projects, Performance & Optimization, Security & Best Practices
  - **Usage**: Add resources as discovered with WHY each is valuable (helps AI know when to use)
  - **Integration**: context-analyzer checks Resources section first before broad web searches
  - **Format**: `[URL] - [Author] - [Why valuable and when to reference]`
  - **Benefits**: Reduces duplicate research, team-curated quality, faster than starting from scratch

- **Investigation Results WORKLOG format** for research documentation (worklog-format.md)
  - **Purpose**: Document external research findings separate from implementation work
  - **Use cases**: context-analyzer research, documentation lookup, resource discovery
  - **Format**: Query → Sources analyzed → Key findings → Top solutions → Curated resources → Suggested for CLAUDE.md
  - **Integration**: Used by context-analyzer agent, `/troubleshoot` Research phase
  - **Variants**: "Investigation Complete" and "Investigation Incomplete" (when no clear solution found)
  - **Benefits**: Audit trail of research, knowledge handoffs documented, supports mixed format WORKLOGs (investigation + troubleshooting + implementation in same file)

- **Review WORKLOG formats** for code and security review documentation (worklog-format.md)
  - **Purpose**: Standardized documentation of code-reviewer and security-auditor findings
  - **Entry types**: "Review Approved" (passes) and "Review Requires Changes" (issues found with handoff)
  - **Structure**: Reviewed → Scope → Verdict → Strengths/Issues → Files
  - **Code reviews**: Quality, performance, testing, best practices issues with severity levels (Critical/Major/Minor)
  - **Security reviews**: OWASP-classified vulnerabilities with file:line references and remediation steps
  - **Integration**: code-reviewer and security-auditor agents create review entries in WORKLOG.md
  - **Benefits**: Review audit trail, clear issue tracking, structured handoffs when fixes needed

- **WORKLOG format decision tree** to help choose correct entry format (worklog-format.md)
  - **Purpose**: Clear guidance on which WORKLOG format to use in different scenarios
  - **Coverage**: Standard (HANDOFF/COMPLETE/REVIEW), Troubleshooting, Investigation formats
  - **Visual tree**: Shows decision flow based on what's being documented
  - **Quick examples**: Agent handoff → HANDOFF, Phase done → COMPLETE, Code review → REVIEW, Debugging → Troubleshooting, Research → Investigation
  - **Benefits**: Reduces format confusion, ensures consistent WORKLOG documentation

- **Coding standards proactive enforcement** - transformed from passive documentation to active quality gate
  - **Status change**: "Optional" → "Active" with automated enforcement
  - **Getting Started checklist**: 40-minute progressive disclosure (5 phases) makes template completion achievable
  - **Common Violations section**: Clear ❌/✅ examples eliminate ambiguity (file naming, identifiers, imports, line length, parameters)
  - **Auto-detection from config files**: Extract standards from .prettierrc, .eslintrc, tsconfig.json, pyproject.toml
  - **Automated Quality Checks section**: File-level, code-level, and style checks with enforcement protocol
  - **Integration with /implement**: Implementation agents read standards BEFORE writing code (prevention vs detection)
  - **Per-Phase quality gate**: Coding standards validation added to development-loop.md (blocks phase completion)
  - **Implementation Agent Protocol**: Detailed before/during/after checklist in implement.md
  - **Benefits**: Consistent code style from day 1, fewer review violations, proactive compliance, clear enforcement

### Changed

- **context-analyzer agent refocused** as external research specialist (prevents context pollution)
  - **OLD role**: Pre-load local project context for all agents (caused context dilution)
  - **NEW role**: External research specialist that curates signal from noise
  - **How it works**: Reads 10-20 external sources (docs, blogs, SO), returns 2-5 page curated summary
  - **Research workflow**: Checks CLAUDE.md Resources section first, then Context7 docs, then WebSearch/WebFetch
  - **Resource suggestions**: When finding exceptional resources during research, suggests adding to CLAUDE.md Resources section (1-3 per session max)
  - **Benefits**: Research happens in separate context window, implementation agents keep clean context
  - **Integration**: Used by `/troubleshoot` Research phase, implementation agents requesting external knowledge
  - **Tools**: WebSearch, WebFetch, Context7 for documentation research
  - **Output**: Focused summaries with curated resource links + suggestions for future-valuable resources
  - **Prevents**: Context window pollution where relevant problem context gets pushed out by generic research
  - Updated 4 commands to remove old context-analyzer references (project-status, docs, security-audit, ui-design)

- **Guideline documentation improvements** after holistic review (16 guideline files reviewed)
  - **Fixed critical issues**:
    - Complete rewrite of context-analyzer section in agent-coordination.md (lines 425-506) - was completely outdated
    - Removed all `/test-fix` references (command removed in v0.19.0) - replaced with `/troubleshoot`
    - Fixed DRY violation: moved security detection criteria to single source of truth in agent-coordination.md
  - **Added missing integrations**:
    - Investigation format references added to development-loop.md and troubleshooting.md
    - context-analyzer integrated into troubleshooting.md Research phase (Option A - recommended approach)
    - Review format references added to development-loop.md quality gates sections
    - context-analyzer added to specialist agent list in development-loop.md
  - **Enhanced clarity**:
    - WORKLOG format decision tree added to worklog-format.md Quick Reference
    - When to invoke context-analyzer with examples (agent-coordination.md)
  - **Files updated**: agent-coordination.md, development-loop.md, troubleshooting.md, worklog-format.md, code-reviewer.md, security-auditor.md
  - **Benefits**: Consistent documentation, accurate agent roles, clear format selection, reduced confusion

- **Guideline metadata standardization** - removed version numbers, date-based tracking only
  - **Rationale**: Guidelines are living documentation, not APIs - version numbers imply discrete releases that don't match reality
  - **Approach**: Date-based tracking with `created` and `last_updated` fields only
  - **Fixed inconsistencies**: Updated 7 files with stale `last_updated` dates (2025-11-06), fixed status capitalization in ui-design-guidelines.md
  - **Documentation**: Added "Guideline Versioning" section to docs/development/README.md explaining when to update dates
  - **Benefits**: Simpler maintenance, clearer freshness tracking, CHANGELOG.md tracks actual changes better than version numbers
  - **Files affected**: All 15 guideline files (removed `version` field from metadata)

### Removed

- **RESEARCH.md file concept** - removed as unnecessary complexity with poor integration
  - **Rationale**: RESEARCH.md was never properly integrated into workflow, no agent created it, no command triggered it
  - **Overlapped with**: ADRs (architecture decisions), WORKLOG entries (simple decisions), Investigation format (external research)
  - **Replaced with**:
    - Simple decisions → WORKLOG entries with brief rationale
    - External research → Investigation format (context-analyzer)
    - Architecture decisions → ADRs via `/adr` command
    - Complex debugging → Extended WORKLOG entries with hypothesis tracking
  - **Removed from**: research-documentation.md guideline (deleted), implement.md, worklog-format.md, development-loop.md, troubleshooting.md, pm-guidelines.md, issue-management.md, plan-structure.md
  - **File count change**: 16 guidelines → 15 guidelines (research-documentation.md removed)
  - **Benefits**: Simpler workflow, clearer decision documentation, reduced cognitive load, better alignment with actual usage patterns

## [0.19.0] - 2025-11-06

### Added

- **`/troubleshoot` command**: Structured 5-step debugging loop (Research → Hypothesize → Implement → Test → Document) - replaces `/test-fix` for systematic debugging
  - **Research-first approach**: Context7 docs lookup, official documentation, user guidance (no guessing)
  - **Liberal debug logging**: Encourages console.log statements at all decision points for evidence gathering
  - **Single hypothesis testing**: One theory at a time with mandatory rollback on failure
  - **Validation required**: Never claims "fixed" without test confirmation and user verification
  - **WORKLOG integration**: Brief entries documenting hypothesis, debug findings, and results
  - **Loop structure**: Repeats until issue resolved, prevents shotgun debugging anti-pattern
  - **Usage**: Standalone bugs (`/troubleshoot BUG-007`) or during tasks (`/troubleshoot`)

### Changed

- **Guideline structure split into focused files** for better organization and targeted loading (16 guideline files now)
  - **Created 4 new guideline files** (1,355 lines total):
    - `worklog-format.md` (347 lines) - **Single source of truth** for both standard and troubleshooting WORKLOG formats
      - Standard format (HANDOFF and COMPLETE entries for implementation work)
      - Troubleshooting format (hypothesis-based entries for debugging)
      - Entry timing, best practices, and cross-referencing RESEARCH.md
    - `plan-structure.md` (330 lines) - Phase patterns, reviews, and progress tracking
      - Default phase structures by task type (frontend, backend, bug fixes, etc.)
      - Mandatory code-architect and conditional security-auditor review requirements
      - Progress tracking protocol (dual PLAN.md + TASK.md tracking)
      - Test-first guidance and agent context briefing patterns
    - `issue-management.md` (410 lines) - EPIC/TASK/BUG file format specifications
      - Complete EPIC.md, TASK.md, BUG.md file structures with YAML frontmatter
      - Issue directory structure and file purposes (TASK.md, PLAN.md, WORKLOG.md, RESEARCH.md)
      - Epic and issue naming conventions, numbering schemes
      - Epic size guidelines (2-8 tasks recommended) and decomposition patterns
    - `research-documentation.md` (268 lines) - When and how to create RESEARCH.md
      - Criteria for RESEARCH.md (3+ alternatives, benchmarks, root cause analysis)
      - RESEARCH.md structure (Problem → Alternatives → Decision → Implementation → References)
      - When to keep decisions in WORKLOG vs creating RESEARCH.md (~500 char rule)
      - Anchor linking from WORKLOG, multiple decisions in same file
  - **Updated existing guideline files** to remove extracted sections and add cross-references:
    - `development-loop.md` (1,188 → ~760 lines, 36% reduction)
      - Removed: WORKLOG format, PLAN structure, progress tracking details, RESEARCH.md format
      - Retained: Core development loop, quality gates, agent coordination
      - Added: Cross-references to new guideline files
    - `pm-guidelines.md` (681 → ~540 lines, 21% reduction)
      - Removed: Epic/TASK/BUG file format specifications, naming conventions
      - Retained: Epic creation workflow, Jira integration patterns, task suggestion
      - Added: Cross-references to issue-management.md
    - `troubleshooting.md` - References worklog-format.md for WORKLOG entry formats
  - **Updated 6 commands** to reference new guideline files:
    - `/troubleshoot`: Reads worklog-format.md instead of development-loop.md
    - `/plan`: Reads plan-structure.md for review requirements and phase patterns
    - `/implement`: Reads plan-structure.md, worklog-format.md, development-loop.md
    - `/worklog`: References worklog-format.md for WORKLOG format
    - `/epic`: References both issue-management.md and pm-guidelines.md
    - Commands now load only relevant content (e.g., `/implement` reads 3 focused files vs 1 monolithic file)
  - **Benefit**: Easier maintenance, focused loading, single responsibility per file

- **All 23 commands standardized** following claude-code-commands-best-practices.md
  - **Added WHAT/WHY/HOW structure** to 7 commands (adr, branch, commit, docs, comment, quality, security-audit)
    - WHAT: One-sentence description of what the command does
    - WHY: When and why to use it
    - HOW: References guideline files for detailed guidance
  - **Added references_guidelines frontmatter** to 7 commands (comment-issue, import-issue, promote, refresh-schema, refresh, toolkit-init, troubleshoot)
    - Machine-readable guideline loading specification
    - Clear comments indicating what each guideline provides
  - **Verified all commands** follow guidelines-first methodology
    - Commands define WHAT (orchestration), guidelines define HOW (configuration)
    - No embedded guideline content in commands
    - All references point to current guideline structure
  - **Benefit**: Consistent command structure, clear guideline references, easier for AI to load correct context

- **WORKLOG format specification updated** in development-loop.md (lines 324-407)
  - **Philosophy**: Stream-based brief entries at handoffs (5-10 lines, ~500 chars ideal)
  - **Ordering**: Reverse chronological (newest entries at TOP) - explicitly enforced
  - **Format**: `[AUTHOR: agent-name] → [NEXT: next-agent]` with Gotcha/Lesson/Files
  - **Entry types**: HANDOFF (passing to another agent) and COMPLETE (phase done)
  - **Best practices**: Focus on insights (WHY), capture alternatives, reference RESEARCH.md for deep dives
  - **Source**: Merged from production usage in quotable project (proven format)
  - **Note**: Troubleshooting has separate format in troubleshooting.md (hypothesis-based structure)

- **All 23 commands optimized** following "Commands = WHAT, Guidelines = HOW" principle
  - **Massive reduction**: 8,277 lines → 3,534 lines (57% reduction, 4,743 lines removed)
  - **Consistent structure**: All commands follow WHAT/WHY/HOW format with concise execution steps
  - **Context preservation**: Commands explicitly load required context via Pre-Execution blocks
  - **Guideline references**: Commands reference HOW details instead of embedding them
  - **Top reductions**: toolkit-init (1,018→241, 76%), ui-design (614→198, 68%), sanity-check (531→128, 76%)
  - **Functionality preserved**: All modes, flags, error handling, and agent coordination intact
  - **Maintainability**: Single source of truth (guidelines), easier to scan and update
  - **Alignment**: Follows best practices from "Claude Code Command Best Practices" research
  - **Pattern**: Pre-execution context loading → Execution steps → Agent coordination → Error handling

## [0.18.0] - 2025-11-05

### Removed

- **`/test-fix` command**: Removed (23 commands remaining)
  - **Rationale**: Redundant with `/implement` workflow - all test failures should go through structured `/plan` → `/implement` process
  - **Why removed**: With "never merge with failing tests" policy, test failures occur either:
    1. During `/implement` (handled by test-engineer agent within the command)
    2. Outside `/implement` (should create BUG-XXX, use `/plan` + `/implement` for structured fixing)
  - **Benefits**: Enforces structured workflow, creates audit trails, eliminates shortcut mentality, reduces command count
  - **Migration path**: Use `/implement` for all test fixing work - even for "simple" fixes, create a plan and implement systematically
  - **Aligned with toolkit philosophy**: Commands orchestrate structured work; convenience shortcuts undermine documentation and quality gates

- **data-analyst agent**: Removed from plugin (18 agents remaining)
  - **Rationale**: Zero command integration, too generic (violated single-responsibility), overlapped with backend-specialist (data processing), database-specialist (queries), and project-manager (ad-hoc tasks)
  - **Migration path**: Statistical/BI analysis handled by external tools (Tableau, Power BI, Jupyter), ad-hoc data tasks handled by project-manager using Context7 for pandas/numpy/scikit-learn
  - **Aligned with research**: Best practices recommend removing overly broad agents, focusing on task-specific specialists

### Changed

- **All 18 agents standardized** following agent-template.md guidelines
  - **Dead references removed**: Eliminated script_integration blocks (test-engineer, technical-writer), ${CLAUDE_PLUGIN_ROOT} references
  - **Structure standardized**: All agents now have consistent Purpose section with guideline references
  - **Guideline references added**: development-loop.md references added to project-manager, test-engineer, technical-writer, migration-specialist
  - **Section naming unified**: All agents use ## Purpose (not # Agent Name headers)
  - **Template created**: plugins/ai-toolkit/docs/agent-template.md for future agent development

- **All 20 agents refactored** following research-based best practices for optimal AI agent design
  - **Action-oriented descriptions**: All agents now have clear "AUTOMATICALLY INVOKED when..." or "Use PROACTIVELY when..." triggers
  - **Streamlined content**: Reduced from 11,064 lines to 5,203 lines (53% average reduction) while maintaining all essential functionality
  - **Context7 integration**: Removed verbose framework catalogs, replaced with Context7 references for latest documentation
  - **Consistent structure**: Unified pattern across all agents (Purpose, Core Responsibilities, Workflow, Output Format, Escalation, Metrics)
  - **Focused workflows**: High-level coordination and essential processes, delegating framework-specific details to Context7
  - **Target achieved**: 19/20 agents now 100-300 lines (average 260 lines), down from average 553 lines
  - **Improved maintainability**: Easier to update, clearer responsibilities, better automatic delegation
  - Based on deep research: "Claude Code Command Best Practices" and "Subagent Best Practices Guide"

- **`/plan` command enhancement**: Made planning more thorough with deep thinking and comprehensive research
  - **Deep thinking phase**: Uses sequential thinking tool to systematically analyze problem, technical approach, and research needs before planning
  - **Library research**: Automatically searches Context7 for latest official documentation of all libraries/frameworks mentioned in task
  - **Best practices research**: Searches web for recent guides, examples, patterns, and common pitfalls (2024-2025 content)
  - **Research synthesis**: Combines official docs, web findings, and project-specific context (ADRs, guidelines) before generating plan
  - **Research summary**: Includes libraries researched, key findings, and best practices applied in plan output
  - **Time expectation**: Now takes 3-5 minutes for thorough analysis and research vs. quick planning
  - **Higher quality**: Plans are more informed, aligned with current best practices, and reference latest documentation

## [0.17.0] - 2025-11-03

### Added

- **`/sanity-check` command**: Mid-work validation to catch drift before it becomes expensive
  - **Deep reflection**: Uses sequential thinking tool to analyze current direction
  - **Context validation**: Reads PLAN.md, WORKLOG.md, guidelines, ADRs, design patterns
  - **Alignment analysis**: Compares work against plan, standards, architecture, design
  - **Concern flagging**: ✅ Green (continue), ⚠️ Yellow (minor fix), 🚩 Red (course correction)
  - **Clear recommendations**: Continue, adjust, correct, or update plan with specific next steps
  - **Use case**: Mid-work pause when complexity increases or something feels off

## [0.16.0] - 2025-11-03

### Added

- **`/refresh` command**: Silently refresh AI context by reading critical project files
  - **Silent operation**: Only outputs `✓ Context refreshed`, no summaries or explanations
  - **Reads**: CLAUDE.md, all guidelines, architecture-overview.md, design-overview.md, last 3 git commits
  - **Use case**: Start of new conversation, after context loss, before planning/implementing
  - **Performance**: Focused on critical files only, completes in under 30 seconds
  - **Read-only**: Never modifies files, purely context loading

## [0.15.0] - 2025-11-03

### Added

- **`/comment-issue` command**: Add AI-suggested comments to external Jira issues based on work context
  - **Interactive mode**: AI analyzes WORKLOG entries, git commits, and existing Jira comments to suggest contextual updates
  - **Direct mode**: User provides comment text, AI shows preview and asks for approval before posting
  - **Context-aware**: Synthesizes recent work into concise, professional updates for stakeholders
  - **External only**: Works with PROJ-### issues, not local TASK-### or BUG-### (use `/worklog` for those)
  - **Always requires approval**: Never auto-posts to Jira, user confirms before any comment is added

### Changed

- **WORKLOG format clarity improvement**: Added explicit `[AUTHOR: ]` and `[NEXT: ]` labels to agent handoff entries
  - Makes it crystal clear who is writing each entry and who receives work next
  - Updated all examples throughout `development-loop.md`
  - Prevents confusion in agent handoff tracking when reviewing project history

## [0.14.0] - 2025-11-03

### Added

- **`/implement --next` flag**: Intelligently determine and execute the next uncompleted phase
  - **Auto-detect task**: Use just `--next` to detect current task from git branch or recent work
  - **Explicit task**: Use `TASK-001 --next` to find next phase for specific task
  - **Smart phase detection**: Finds first uncompleted checkbox in PLAN.md
  - **User confirmation**: Shows next phase and asks for confirmation before executing
  - **Flexible options**: User can approve, decline, or specify a different phase
  - **Completion detection**: Detects when all phases complete and offers task validation
  - **Workflow integration**: Continues with standard execution flow after phase determination

### Changed

- **`/plan` command mandatory context review**: Enforces reading all project context before creating plans
  - **CRITICAL step 0**: MUST read CLAUDE.md, all guidelines, architecture-overview.md, design-overview.md BEFORE planning
  - **Project configuration**: Read CLAUDE.md for tech stack, APIs, deployment, team conventions
  - **All guidelines**: Read ALL files in docs/development/guidelines/ (not just development-loop.md)
  - **Architecture overview**: Read approved architectural decisions to align plans with existing ADRs
  - **Design overview**: Read approved UI/UX patterns to reference in plans
  - **"Why this is CRITICAL" section**: Explains consequences of skipping context (contradicting ADRs, ignoring design patterns, wrong thresholds)
  - **Failure consequences listed**: Plans contradict architecture, miss ADRs, use wrong complexity rules, ignore approved designs, incompatible with tech stack
  - **Prevents**: Plans that contradict existing decisions, ignore established patterns, use generic instead of project-specific approaches

- **`/implement` command guideline enforcement**: Significantly strengthened enforcement of `development-loop.md` guideline
  - **CRITICAL step 0**: MUST read `development-loop.md` completely before ANY other steps
  - **Mandatory re-reads**: Each execution step requires re-reading relevant guideline sections
  - **Specific violations listed**: Documents common violations at each step (missing WORKLOG entries, wrong formats, skipped quality gates)
  - **"Why this matters" section**: Explains consequences of skipping guideline (incomplete implementations, workflow violations)
  - **No assumptions allowed**: Command explicitly states "do NOT assume defaults - read the actual file"
  - **Project-specific emphasis**: Reinforces that every project customizes the guideline
  - **Quality threshold enforcement**: Must check guideline for actual thresholds (not assume ≥ 90)
  - **WORKLOG format enforcement**: Must follow exact format from guideline (timestamps, handoffs, completion entries)
  - **Security detection enforcement**: Must use guideline criteria for security-relevant phase detection
  - **Prevents**: Skipped quality gates, missing WORKLOG entries, wrong formats, incomplete implementations

- **`/plan` command template enforcement**: Command now explicitly follows `pm/templates/plan.md` for concise plan format
  - **Reads template**: Explicitly reads `pm/templates/plan.md` before generating plan
  - **Concise format**: Plans are simple checklists (numbered items), not verbose documentation
  - **Clear examples**: Shows good vs bad phase formatting to prevent over-documentation
  - **Key principle**: PLAN.md is a roadmap, details worked out during `/implement`
  - **Prevents issue**: Stops generation of overly verbose plans with excessive detail, code examples, and lengthy explanations

## [0.13.0] - 2025-11-03

### Added

- **Versioning and Releases Guideline**: New `versioning-and-releases.md` guideline for semantic versioning, release process, and CHANGELOG maintenance
  - **Comprehensive semver guide**: Covers MAJOR.MINOR.PATCH rules, pre-1.0.0 phase strategy, version increment examples
  - **Release workflows**: Documents development releases (0.x.y on develop) and production releases (1.0.0+ on main)
  - **Git tagging strategy**: Annotated tags, naming conventions, when/where to tag
  - **CHANGELOG best practices**: Keep a Changelog format, category guidelines, writing style, unreleased section maintenance
  - **Version decision tree**: Quick reference for determining version increments
  - **Template count update**: Starter template now has 37 files (was 34), 8 guidelines (was 7)
  - **Cross-references added**: Updated CLAUDE.md, git-workflow.md, development-loop.md to reference new guideline
  - **Repository versioning**: Updated root CLAUDE.md to follow these versioning guidelines for plugin development

- **`/changelog` Command**: Automatically check and update CHANGELOG.md with undocumented changes
  - **Git analysis**: Analyzes commits since last release to detect undocumented changes
  - **Change detection**: Identifies new features, bug fixes, breaking changes from git history
  - **Smart categorization**: Suggests properly formatted CHANGELOG entries (Added, Changed, Fixed, Removed)
  - **User-focused writing**: Focuses on user impact rather than implementation details
  - **Keep a Changelog format**: Follows standard format with [Unreleased] section updates
  - **Proactive use**: Run before PRs, after features, or when preparing releases
  - **References guidelines**: Uses `versioning-and-releases.md` for CHANGELOG format and style

- **`/release` Command**: Release new versions following semantic versioning guidelines
  - **Interactive version suggestion**: Analyzes [Unreleased] changes and suggests version (MAJOR.MINOR.PATCH)
  - **Pre-1.0.0 awareness**: Handles pre-1.0.0 rules (MINOR for features/breaking, PATCH for bugs)
  - **Multiple formats**: `/release` (interactive), `/release 0.13.0` (explicit), `/release minor` (keyword)
  - **CHANGELOG release**: Moves [Unreleased] to versioned section with release date
  - **Version file updates**: Updates all version references (marketplace.json, plugin.json, README.md, CLAUDE.md)
  - **Annotated git tags**: Creates tags with release notes extracted from CHANGELOG
  - **Pre-flight checks**: Validates clean git status, correct branch, non-empty [Unreleased]
  - **References guidelines**: Uses `versioning-and-releases.md` for semver rules and release process

- **Epic bulk import in `/import-issue`**: Import Jira epics with all child issues in one command
  - **Epic detection**: When importing an epic, fetches all child issues from Jira
  - **Bulk import prompt**: Asks "Import all N child issues?" after displaying epic details
  - **Progress tracking**: Shows import progress for each child issue
  - **Smart skipping**: Skips already-imported issues, shows summary at end
  - **Epic directory**: Creates `pm/epics/PROJ-###-name/` instead of `pm/issues/` for epics
  - **Child issue list**: Displays all child issues with types and statuses

### Changed

- **`/ui-design` Playwright review requirement**: Command now requires browser testing before presenting mockups to users
  - **Step 4 added**: "Review Mockups with Playwright" - load each HTML mockup in browser
  - **Validation checks**: Broken layouts, missing styles, JavaScript errors, console warnings
  - **Responsive testing**: Verify behavior at key breakpoints (320px, 768px, 1280px)
  - **Interactive validation**: Test buttons, forms, dropdowns work correctly
  - **Iteration reviews**: Run Playwright review again after each design iteration
  - **Best practice added**: "Always review with Playwright" in command best practices

- **`/epic` Jira detection improvements**: More explicit Jira configuration checking to prevent local file creation when Jira is enabled
  - **Critical first step**: ALWAYS read CLAUDE.md to check Jira configuration before doing anything
  - **Mode confirmation**: Display to user whether creating in Jira or locally after detection
  - **Explicit reminders**: Multiple reminders that Jira-enabled means epics go to Jira ONLY, no local files
  - **Updated pm-guidelines.md**: New "Detecting Jira Mode" section with step-by-step detection process
  - **Prevents confusion**: User sees confirmation of mode before epic creation begins

## [0.12.0] - 2025-11-03

### Added

- **CHANGELOG Synchronization**: Plugin now includes its own CHANGELOG.md copy for update/sync process
  - **Plugin CHANGELOG**: Added `plugins/ai-toolkit/CHANGELOG.md` (copy of root CHANGELOG)
  - **Required for `/toolkit-init`**: Update/sync mode reads plugin's CHANGELOG to show version-specific changes
  - **Sync process documented**: CLAUDE.md now includes instructions to keep both CHANGELOGs in sync
  - **Command**: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md` after each CHANGELOG update
  - Files updated: `plugins/ai-toolkit/CHANGELOG.md` (new), `CLAUDE.md` (added sync process)

- **Toolkit Version Tracking**: Projects now track which ai-toolkit version they use for intelligent updates
  - **Version metadata in CLAUDE.md**: Plugin version and last update timestamp automatically populated during init
  - **CHANGELOG-driven updates**: `/toolkit-init` compares project version to plugin version and shows relevant CHANGELOG sections
  - **Smart version parsing**: Extracts and displays only changes between project's version and current plugin version
  - **Breaking change detection**: Highlights breaking changes in red before proceeding with updates
  - **Affected file correlation**: Shows which template files were impacted by each version's changes
  - **Auto-version update**: After successful update, CLAUDE.md automatically updated with new plugin version and timestamp
  - **Benefits**: Users see exactly what changed between versions, make informed update decisions, track toolkit evolution
  - Files updated: `templates/starter/CLAUDE.md` (added version section), `commands/toolkit-init.md` (added version detection and CHANGELOG parsing logic)

- **`/ui-design` Command**: Interactive HTML mockup generation with parallel design exploration
  - **Conversational workflow**: Natural language interaction for design creation and iteration
  - **Parallel variants**: Generate N designs simultaneously ("show me 4 options"), compare approaches
  - **Single-file HTML**: Self-contained mockups (inline CSS/JS, embedded assets as data URLs)
  - **Flat structure**: Simple file organization (`{name}-v{N}.html`, `{name}-v{N}{letter}.html` for variants)
  - **Tech-specific mockups**: Request mockups using component libraries ("using shadcn", "with chakra-ui")
    - Filename: `{name}-v{N}-{tech}.html` (e.g., `login-v1-shadcn.html`)
    - Visual preview: HTML renders in browser
    - Implementation code: Embedded in HTML comments as copy-paste ready React/Vue code
    - Component library comparison: Generate multiple tech versions side-by-side for evaluation
    - shadcn MCP integration: Uses shadcn MCP for component-aware generation when requested
  - **Approval tracking**: `design-overview.md` tracks approved designs (mirrors `architecture-overview.md` pattern)
  - **Agent coordination**: ui-ux-designer primary, generates HTML mockups following `ui-design-guidelines.md`
  - **Component patterns**: Separate `components/` directory for reusable UI patterns
  - **Integration**: Reference approved designs in tasks, use during `/implement` for visual reference
  - Commands: create, iterate, approve, list (all conversational)
  - Files: `commands/ui-design.md`, `ui-design-guidelines.md`, `design-overview.md`, READMEs
  - **frontend-specialist integration**: Agent detects tech-specific mockups and extracts implementation code from HTML comments

### Changed

- **Design directory restructure**: Renamed `design/` to `design-assets/` for clarity
  - **Removed obsolete subdirectories**: Deleted `mockups/`, `screenshots/`, `color-schemes/` (replaced by `/ui-design` workflow)
  - **Focused on static assets**: `design-assets/` now only contains brand assets (logos, icons, fonts)
  - **Clear separation**: HTML mockups in `ui-designs/` via `/ui-design`, static assets in `design-assets/`
  - **Updated documentation**: `design-assets/README.md` now references `/ui-design` workflow and new structure

### Removed

- **`design-system.md`**: Replaced by two-file approach for better workflow integration
  - **`ui-design-guidelines.md`**: Design tokens, breakpoints, accessibility standards (configuration)
  - **`design-overview.md`**: Approved designs, extracted patterns (living synthesis document)
  - **Rationale**: Separates configuration (guidelines) from discovered patterns (overview), mirrors `architecture-overview.md` approach

## [0.11.2] - 2025-11-02

### Changed

- **WORKLOG Stream Pattern**: Changed WORKLOG documentation from summary-style to stream-style entries
  - **Write at handoffs**: Agents write entries when passing work between agents (not post-phase summaries)
  - **Cross-agent handoffs only**: Entries for backend→code-reviewer, code-reviewer→backend, etc.
  - **Brief entries**: 5-10 lines per entry (details in git diffs, not WORKLOG)
  - **Entry types**: HANDOFF entries (`agent-name → next-agent`) and COMPLETE entries (`agent-name (Phase X.Y COMPLETE)`)
  - **Shows work flow**: WORKLOG reads like conversation between agents, not retrospective reports
  - **Enforced in /implement**: Command explicitly prompts for WORKLOG entries before agent handoffs
  - Commands affected: `/implement` (added explicit WORKLOG handoff steps)
  - Guidelines affected: `development-loop.md` (rewrote WORKLOG Entry Format section with stream pattern)

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

- **`/worklog` command**: Human-AI collaboration through timestamped work log entries
  - Add manual work notes to WORKLOG.md (e.g., `/worklog "Added login button to header"`)
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
  - **Bidirectional**: Both AI agents (via `/implement`) and humans (via `/worklog`) add entries
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

- **BREAKING: Command consolidation** - 21 → 14 commands (added `/worklog`)
  - Consolidated: `/docs-*` (5 commands) → `/docs` (natural language)
  - Removed: `/review` (→ `/quality`), `/refresh` (→ `/status`), `/design` (redundant), `/improve` (obsolete)
  - Added: `/worklog` for human work log entries

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
