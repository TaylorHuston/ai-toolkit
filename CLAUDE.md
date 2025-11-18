---
version: "0.29.0"
created: "2025-08-21"
last_updated: "2025-11-17"
status: "active"
target_audience: ["ai-assistants"]
document_type: "specification"
priority: "critical"
tags: ["plugin-development", "workflow", "standards"]
---

# CLAUDE.md - AI Toolkit Plugin Development

You are working on the AI Toolkit plugin repository for Claude Code. This CLAUDE.md provides instructions for **developing and maintaining the plugin itself**, not for using it in projects.

## Project Context

- **Repository Type**: Claude Code plugin marketplace
- **Main Plugin**: AI Toolkit (26 commands, 21 agents, starter templates)
- **Purpose**: Develop and maintain plugin code, documentation, and templates
- **Repository Structure**:
  - `.claude-plugin/marketplace.json` - Marketplace metadata
  - `plugins/ai-toolkit/` - Plugin source code
  - `plugins/ai-toolkit/.claude-plugin/plugin.json` - Plugin metadata
  - `plugins/ai-toolkit/commands/` - 26 command files (.md)
  - `plugins/ai-toolkit/agents/` - 21 agent files (.md)
  - `plugins/ai-toolkit/templates/starter/` - Project templates (29 files)
  - `plugins/ai-toolkit/docs/` - Plugin documentation

## Core Development Principles

- **DRY** (Don't Repeat Yourself)
- **KISS** (Keep It Simple)
- **YAGNI** (You Aren't Going To Need It)
- **SOLID** principles
- **Single Source Of Truth**
- **Never claim completion without >95% confidence**

## Critical Rules

1. **Commit Approval**: Never commit without explicit user approval
2. **Deletion Approval**: Always ask before file/branch deletions
3. **Test Locally**: Test all plugin changes locally before committing
4. **File Naming**: Use lowercase-kebab-case for all files
5. **No Assumptions**: Check existing patterns, ask if uncertain
6. **Documentation**: Update CHANGELOG.md for all user-facing changes

## Plugin Development Workflow

### Local Testing

```bash
# Install this marketplace locally
/plugin marketplace add /path/to/ai-toolkit

# Install the plugin
/plugin install ai-toolkit

# Make changes to commands/agents/templates
# Changes take effect on next plugin reload
```

### Editing Plugin Components

- **Commands**: Edit `.md` files in `plugins/ai-toolkit/commands/`
- **Agents**: Edit `.md` files in `plugins/ai-toolkit/agents/`
- **Templates**: Modify files in `plugins/ai-toolkit/templates/starter/`
- **Plugin Docs**: Update `plugins/ai-toolkit/README.md` and `plugins/ai-toolkit/docs/`

### Version Management

**Currently in pre-1.0.0 phase** - Breaking changes allowed, increment MINOR for features/breaking changes, PATCH for bug fixes only.

- **Format**: Semantic versioning (MAJOR.MINOR.PATCH) following [Semver 2.0.0](https://semver.org/)
- **Pre-1.0.0 Rules**:
  - `0.x.0` - New features, enhancements, or breaking changes
  - `0.0.x` - Bug fixes only (no new features)
  - Breaking changes document clearly in CHANGELOG under `### Changed` or `### Breaking`
- **Version Increment Examples**:
  - New command added → `0.12.0` → `0.13.0` (MINOR)
  - Bug fix in command → `0.12.0` → `0.12.1` (PATCH)
  - Breaking template change → `0.12.0` → `0.13.0` (MINOR, note as breaking)
- **Update In**:
  - `.claude-plugin/marketplace.json` (marketplace version and plugin version)
  - `plugins/ai-toolkit/.claude-plugin/plugin.json` (plugin version)
  - Root `README.md` (version reference)
  - Root `CLAUDE.md` (version and last_updated in frontmatter)
- **Document**: Always update CHANGELOG.md following [Keep a Changelog](https://keepachangelog.com/) format
- **Git Tagging**:
  - Create annotated tags for all releases: `git tag -a v0.13.0 -m "Release v0.13.0: [description]"`
  - Push tags: `git push origin --tags`
  - Tag format: `v0.x.y` (lowercase v prefix)

## Documentation Standards

### Files to Maintain

- **CHANGELOG.md**: All changes (Added, Changed, Removed, Breaking)
  - **CRITICAL**: Must maintain TWO copies in sync:
    - Root: `CHANGELOG.md` (repository changelog)
    - Plugin: `plugins/ai-toolkit/CHANGELOG.md` (used by `/toolkit-init` update process)
  - **Process**: Update root CHANGELOG.md first, then copy to plugin directory
  - **Command**: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md`
- **README.md**: Marketplace overview, accurate counts (commands, agents, files)
- **plugins/ai-toolkit/README.md**: Plugin documentation and usage
- **plugins/ai-toolkit/docs/COMMANDS.md**: Command reference
- **plugins/ai-toolkit/docs/AGENTS.md**: Agent reference
- **STATUS.md**: Current development status and priorities

### Documentation Accuracy

- **Counts Must Match**: Command count, agent count, template file count
- **No Broken Links**: All file path references must be valid
- **Consistency**: README, CHANGELOG, and code must align
- **GitHub Username**: Use "TaylorHuston" consistently (not "taylorh140")

## Quality Standards

- Follow existing patterns in command/agent files
- Keep documentation synchronized with code changes
- Test command changes in actual usage scenarios
- Ensure template files work for project initialization
- Validate that all references point to existing files
- Check for outdated information after structural changes

## Response Protocol

- **Be Concise**: Show code/commands, explain only when asked
- **Be Explicit**: Confirm understanding before major changes
- **Be Incremental**: Work in small, atomic commits
- **Be Transparent**: Report errors immediately with context
- **Be Evidence-Based**: Verify claims with actual file checks

## Common Development Tasks

### Adding a New Command

1. Create `.md` file in `plugins/ai-toolkit/commands/`
2. Update command count in README.md, plugin.json
3. Update `plugins/ai-toolkit/docs/COMMANDS.md`
4. Update root CHANGELOG.md (add to `[Unreleased] > Added`)
5. Copy CHANGELOG: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md`
6. Test locally
7. **When releasing**: Increment MINOR version (0.12.0 → 0.13.0)

### Adding a New Agent

1. Create `.md` file in `plugins/ai-toolkit/agents/`
2. Update agent count in README.md
3. Update `plugins/ai-toolkit/docs/AGENTS.md`
4. Update root CHANGELOG.md (add to `[Unreleased] > Added`)
5. Copy CHANGELOG: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md`
6. Test agent invocation
7. **When releasing**: Increment MINOR version (0.12.0 → 0.13.0)

### Updating Templates

1. Modify files in `plugins/ai-toolkit/templates/starter/`
2. Update file count if adding/removing files
3. Test with `/toolkit-init` in a test project
4. Update root CHANGELOG.md (add to `[Unreleased] > Changed` or `Added`)
5. Copy CHANGELOG: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md`
6. Update documentation if structure changed
7. **When releasing**: Increment MINOR version (0.12.0 → 0.13.0)

### Bug Fixes Only

1. Fix the bug in command/agent/template
2. Update root CHANGELOG.md (add to `[Unreleased] > Fixed`)
3. Copy CHANGELOG: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md`
4. Test thoroughly
5. **When releasing**: Increment PATCH version (0.12.0 → 0.12.1)

### Making Breaking Changes

1. Mark clearly in CHANGELOG.md as **BREAKING** under `### Changed`
2. Document migration path for users
3. Update all affected documentation
4. Copy CHANGELOG: `cp CHANGELOG.md plugins/ai-toolkit/CHANGELOG.md`
5. Test thoroughly before release
6. **When releasing**: Increment MINOR version in pre-1.0.0 (0.12.0 → 0.13.0)

## Priority When Uncertain

1. Documentation accuracy and completeness
2. Consistency across all files
3. No broken references or outdated information
4. Version number alignment
5. CHANGELOG completeness

Follow these instructions precisely when working on the AI Toolkit plugin repository.
