# Project Context

Welcome to your AI-assisted project! This file provides essential context to Claude Code about your project.

## Project Information

- **Project Name**: {app-name}
- **Description**: {description}

## Tech Stack

**Update as you decide on technologies:**

- **Frontend**: [e.g., React, Vue, Next.js - update when decided]
- **Backend**: [e.g., Node.js, Python/Django, Go - update when decided]
- **Database**: [e.g., PostgreSQL, MongoDB - update when decided]
- **Infrastructure**: [e.g., AWS, Docker, Kubernetes - update when decided]

## External Links

**Add URLs when available:**

- **Project Management**: [Jira/Linear/GitHub Projects]
- **Wiki/Documentation**: [Confluence/Notion/GitBook]
- **CI/CD**: [GitHub Actions/Jenkins/CircleCI]
- **Monitoring**: [Datadog/New Relic/Sentry]

## AI Toolkit Configuration

**Toolkit Version Information:**

- **Plugin Version**: {toolkit-version}
- **Last Updated**: {last-updated}
- **Template Customizations**: None (update this list when you customize guideline files)

## Jira Integration

**Optional**: Enable Jira integration for teams using Jira as their project management system.

### Configuration

When Jira integration is enabled, epics and issues can be created and managed in Jira through AI commands.

```yaml
## Jira Integration
- **Enabled**: false  # Set to true to enable Jira integration
- **MCP Server**: Atlassian Remote MCP
- **Project Key**: PROJ  # Your Jira project key (e.g., PROJ, ENG, PRODUCT)
```

### Requirements

- **Atlassian Remote MCP Server** configured in Claude Code (see [setup guide](https://www.atlassian.com/blog/announcements/remote-mcp-server))
- Jira Cloud account with appropriate permissions
- Project Key for your Jira project

### How It Works

**When Jira is DISABLED (default):**
- Epics: `pm/epics/EPIC-001-name.md` (local files)
- Issues: `pm/issues/TASK-001/`, `pm/issues/BUG-001/` (local directories)
- Fully local, works offline

**When Jira is ENABLED:**
- Epics: Only in Jira (PROJ-100, PROJ-200) - no local epic files
- Issues:
  - Jira issues: `PROJ-123`, `PROJ-124` (fetched from Jira on-demand)
  - Local exploration: `TASK-001`, `BUG-001` (for quick spikes, can be promoted to Jira)
- Local storage: Only `PLAN.md`, `WORKLOG.md`, `RESEARCH.md` (no TASK.md for Jira issues)

### Commands

**Creating Epics:**
```bash
/epic  # Creates PROJ-100 in Jira (if enabled) or EPIC-001 locally (if disabled)
```

**Working with Issues:**
```bash
/import-issue PROJ-123    # Import Jira issue for local work
/plan PROJ-123            # Create implementation plan (fetches from Jira)
/implement PROJ-123 1.1   # Execute phase
```

**Exploration Workflow:**
```bash
/plan TASK-001            # Quick local spike
/implement TASK-001 1.1   # Prototype
/promote TASK-001         # Promote to Jira when validated
```

### What Syncs

- ✅ **Read from Jira**: Description, acceptance criteria, status (display only)
- ✅ **Create in Jira**: Epics, stories, tasks via AI conversation
- ❌ **Not synced**: Status updates, comments, field changes

**Note**: Update Jira status manually in the Jira UI. The toolkit reads from Jira but doesn't write status updates back.

### Field Discovery

The AI automatically discovers which fields are required in your Jira instance (including custom fields) and prompts for them conversationally during epic/issue creation. Field schemas are cached locally in `.ai-toolkit/jira-field-cache.json` for performance.

Run `/refresh-schema` if Jira administrators change required fields.

## Product Vision

See `docs/project-brief.md` for:
- Problem statement and solution approach
- Target audience and use cases
- Core features and success metrics

Run `/project-brief` to fill in your brief through conversation.

## Development Workflow

This project uses the AI Toolkit plugin for structured development with three-branch git workflow:

```bash
# 1. Complete project brief
/project-brief              # Fill in product vision through conversation

# 2. Create feature epics
/epic                       # Create epics aligned with your brief

# 3. Make architecture decisions
/adr                  # Create ADRs for technical decisions

# 4. Plan implementation
/plan TASK-001              # Add implementation details to tasks

# 5. Build features
/implement TASK-001 1.1     # Execute specific phase (creates feature/TASK-001 branch)
/implement TASK-001 1.2     # Continue implementation
/commit                     # Branch-aware commit (includes TASK-001 reference)

# 6. Quality gates and merge
/quality                    # Comprehensive quality assessment
/test-fix                   # Fix failing tests (if needed)
/branch merge develop       # Merge to staging (runs tests, blocks if fail)

# 7. Promote to production
/branch switch develop      # Switch to staging branch
/branch merge main          # Merge to production (validates staging health)

# 8. Clean up
/branch delete feature/TASK-001  # Delete work branch after merge
```

### Git Workflow (Three-Branch Model)

**CRITICAL RULES:**
- ✅ Work branches (feature/*, bugfix/*) → **develop ONLY**
- ✅ **Only develop** → main
- ❌ **NEVER** merge work branches directly to main

```
main (production)         # Live environment - ONLY from develop
  ↑
  └─ develop (staging)    # Pre-production - ONLY from feature branches
       ↑
       ├─ feature/TASK-001  (your work)
       └─ bugfix/BUG-003    (your work)
```

**Merge Gates:**
- feature/* → develop: All tests MUST pass
- develop → main: Staging environment MUST be validated
- feature/* → main: **BLOCKED** (use develop)

**Emergency Hotfixes:** Document in ADR, use hotfix/* prefix, backport to develop

See `docs/development/guidelines/git-workflow.md` for complete workflow rules

### CHANGELOG Maintenance

**IMPORTANT**: Keep `CHANGELOG.md` updated following [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) format.

**When to update CHANGELOG:**
- After implementing features (add to "Unreleased > Added")
- After fixing bugs (add to "Unreleased > Fixed")
- After making breaking changes (add to "Unreleased > Changed" with BREAKING CHANGE note)
- Before releases (move Unreleased content to new version section)

**AI agents should:**
- Remind about CHANGELOG updates after completing work
- Suggest appropriate category (Added/Changed/Fixed/Removed)
- Draft changelog entries based on work completed
- Never commit without CHANGELOG update (unless trivial change)

**Format:**
```markdown
## [Unreleased]

### Added
- New feature description with context

### Changed
- BREAKING: Description of breaking change

### Fixed
- Bug fix description with issue reference
```

## Development Guidelines

Your project includes **7 customizable guideline templates** in `docs/development/guidelines/`:

- `development-loop.md` - AI-assisted development workflow and quality gates
- `api-guidelines.md` - API patterns and structure
- `testing-standards.md` - Testing approach
- `git-workflow.md` - Branching and commits
- `coding-standards.md` - Code style and conventions
- `security-guidelines.md` - Security practices
- `architectural-principles.md` - Design philosophy

These guidelines:
- **Start with TBD placeholders** (filled in as you make decisions)
- **Configure how AI agents work** (agents read them to adapt behavior)
- **Are customizable** (edit to fit your project's needs)
- **Evolve with your project** (living documentation)

See `docs/development/README.md` for details on how guidelines work.

## Need Help with AI Toolkit?

**Ask Claude** - Claude has access to complete plugin documentation:

- "What agents are available and what do they do?"
- "How does the /adr command work?"
- "Show me the full command workflow"
- "What's the difference between /epic and /plan?"
- "How do I customize guidelines?"

Claude reads the plugin documentation and provides detailed explanations.

## Project-Specific Notes

Add any project-specific conventions or guidelines here:

### Coding Conventions
- [Add language-specific style guides]
- [Naming conventions unique to this project]
- [Module organization patterns]

### Testing Strategy
- [Test coverage targets]
- [Testing frameworks and tools]
- [CI/CD integration]

### Deployment
- [Deployment process]
- [Environment configuration]
- [Release procedures]

---

**Note**: This file is read by Claude Code to understand your project. Keep it current as your project evolves.

**Need help?** See `GETTING-STARTED.md` for how to use the AI Toolkit effectively!
