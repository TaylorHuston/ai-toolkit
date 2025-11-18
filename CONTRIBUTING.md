# Contributing to AI Toolkit

Thank you for your interest in contributing to the AI Toolkit project! This document provides guidelines for contributing to this repository.

## Quick Start

1. **Read the Documentation**: Start with [README.md](./README.md) for project overview
2. **Review Guidelines**: Check the starter template in [plugins/ai-toolkit/templates/starter/](./plugins/ai-toolkit/templates/starter/) to understand the project structure
3. **Understand the Workflow**: Familiarize yourself with the [3-phase workflow](./README.md#-core-3-phase-workflow) (Architecture → Planning → Execution)

## Types of Contributions

### 🐛 Bug Reports
- Use GitHub Issues with clear reproduction steps
- Include system information and error messages
- Specify plugin version and Claude Code version

### ✨ Feature Requests
- Check existing issues to avoid duplicates
- Describe the use case and problem being solved
- Include implementation considerations if applicable

### 📖 Documentation Improvements
- Update documentation in user projects via `/toolkit-init` template
- Maintain consistency across:
  - Root README.md (marketplace overview)
  - plugins/ai-toolkit/README.md (plugin documentation)
  - Template files in plugins/ai-toolkit/templates/starter/
- Test that all links resolve correctly

### 🔧 Code Contributions
- Follow the project's established patterns and conventions
- Update CHANGELOG.md following [Keep a Changelog](https://keepachangelog.com/) format
- Test changes with local plugin installation

## Development Process

### 1. Setup

```bash
# Clone the repository
git clone https://github.com/TaylorHuston/ai-toolkit
cd ai-toolkit

# Install as local marketplace
/plugin marketplace add ./

# Install the plugin locally
/plugin install ai-toolkit

# Test in a sample project
cd /path/to/test-project
/toolkit-init
```

### 2. Workflow

**For Plugin Development:**
- Use feature branches for all changes
- Follow the `/project-brief → /spec → /adr → /plan → /implement` workflow for substantial changes
- Commands and agents are in `.md` files - edit directly and test

**For Template Improvements:**
- Templates are in `plugins/ai-toolkit/templates/starter/`
- Test with `/toolkit-init` in a clean project
- Verify all 4 guideline directories work correctly:
  - `conventions/` (7 files)
  - `workflows/` (9 files)
  - `templates/` (12 files)
  - `misc/` (4 files)

### 3. Quality Standards

- **Documentation**: Keep all README files synchronized
- **Counts**: Verify command count (25), agent count (21), template file count (49) are accurate
- **Paths**: All file path references must be correct
- **CHANGELOG**: Update for all user-facing changes

## AI-Assisted Development

This project is optimized for AI-assisted development using Claude Code:

### Using the Agent System

- **21 Specialized Agents**: Use appropriate agents for domain expertise
  - Strategic: project-manager, security-auditor, brief-strategist, ai-llm-expert
  - Implementation: frontend-specialist, backend-specialist, database-specialist, etc.
  - Quality: code-reviewer, test-engineer, technical-writer
- **Context Management**: Agents read project guidelines automatically
- **Multi-Model**: Strategic decisions use Opus 4.1, implementation uses Sonnet 4.5

### Best Practices

- Use `/plan` for complex changes to get multi-agent analysis
- Leverage the technical-writer agent for documentation updates
- Use the code-reviewer agent before submitting changes
- Test command changes in actual usage scenarios

## Submission Guidelines

### Pull Requests

1. **Clear Description**: Explain what changes were made and why
2. **Linked Issues**: Reference related issues using keywords (fixes #123, closes #456)
3. **Updated Documentation**:
   - Root README.md (if marketplace-level changes)
   - plugins/ai-toolkit/README.md (if plugin changes)
   - CHANGELOG.md (all user-facing changes)
   - Template files (if structure changes)
4. **Version Alignment**: Ensure all version numbers match if releasing
5. **AI Attribution**: Note if AI tools were used significantly in commits

### Commit Messages

Follow conventional commit format with AI attribution:

```
type(scope): description

- Detailed change 1
- Detailed change 2

🤖 Generated with Claude Code

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Types**: feat, fix, docs, refactor, chore, test
**Scopes**: commands, agents, templates, docs, workflow

### CHANGELOG Updates

**Required for all user-facing changes:**

```markdown
## [Unreleased]

### Added
- New feature description

### Changed
- BREAKING: Breaking change description (if applicable)
- Enhancement description

### Fixed
- Bug fix description

### Removed
- Removed feature description
```

## Contributing to Different Parts

### Plugin Commands (plugins/ai-toolkit/commands/)

1. Edit the `.md` file for the command
2. Update command count in marketplace.json if adding/removing
3. Test the command in a real project scenario
4. Update docs/development/misc/commands.md template if needed
5. Add CHANGELOG entry

### Plugin Agents (plugins/ai-toolkit/agents/)

1. Edit the `.md` file for the agent
2. Update agent count in marketplace.json and README files if adding
3. Define clear triggers and tool access
4. Update docs/development/misc/agents.md template if needed
5. Add CHANGELOG entry

### Starter Template (plugins/ai-toolkit/templates/starter/)

1. Modify files in the template structure
2. Update file counts if adding/removing files
3. Test with `/toolkit-init` in clean project
4. Ensure 4-directory structure remains intact
5. Update GETTING-STARTED.md if user experience changes
6. Add CHANGELOG entry

### Documentation

**Three documentation locations:**

1. **Root README.md**: Marketplace overview, installation, high-level features
2. **plugins/ai-toolkit/README.md**: Plugin details, technical documentation
3. **Template Documentation**: Files copied to user projects via `/toolkit-init`
   - GETTING-STARTED.md
   - docs/development/README.md
   - docs/development/misc/*.md

Keep these synchronized when making structural changes.

## Review Process

### What We Check

- **Correctness**: All file paths, counts, and references are accurate
- **Consistency**: Documentation aligns across all locations
- **Quality**: Changes follow established patterns
- **Testing**: Changes work in real usage scenarios
- **CHANGELOG**: All user-facing changes documented

### Version Updates

When releasing (done by maintainers):

1. Update version in:
   - `.claude-plugin/marketplace.json`
   - `plugins/ai-toolkit/.claude-plugin/plugin.json`
   - `CLAUDE.md` (version and last_updated)
2. Transform CHANGELOG [Unreleased] → [version] - date
3. Copy CHANGELOG to `plugins/ai-toolkit/CHANGELOG.md`
4. Create annotated git tag: `git tag -a vX.Y.Z -m "Release vX.Y.Z"`
5. Push: `git push origin main --tags`

## Community Standards

### Code of Conduct

- Be respectful and inclusive in all interactions
- Focus on constructive feedback and collaboration
- Help maintain a welcoming environment for all contributors

### Communication

- Use GitHub Issues for bug reports and feature requests
- Use GitHub Discussions for questions and general help
- Be clear and specific in all communications
- Provide context and examples when reporting issues

## Getting Help

### Resources

- **Main Documentation**: [README.md](./README.md) - Marketplace overview and quick start
- **Plugin Documentation**: [plugins/ai-toolkit/README.md](./plugins/ai-toolkit/README.md) - Complete plugin documentation
- **Starter Template**: [plugins/ai-toolkit/templates/starter/](./plugins/ai-toolkit/templates/starter/) - Project template structure
- **Command Reference**: Created in user projects as `docs/development/misc/commands.md` after `/toolkit-init`
- **Agent Reference**: Created in user projects as `docs/development/misc/agents.md` after `/toolkit-init`

### Support Channels

- **GitHub Issues**: For bugs and feature requests
- **GitHub Discussions**: For questions and general help
- **Documentation**: Self-service guides for most scenarios

## Project Structure for Contributors

```
ai-toolkit/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace metadata, plugin version
├── CHANGELOG.md                   # Root changelog (primary)
├── CONTRIBUTING.md                # This file
├── README.md                      # Marketplace overview
├── CLAUDE.md                      # Plugin development guide
└── plugins/
    └── ai-toolkit/
        ├── .claude-plugin/
        │   └── plugin.json        # Plugin metadata, version
        ├── CHANGELOG.md           # Synced from root
        ├── README.md              # Plugin documentation
        ├── commands/              # 25 command files
        ├── agents/                # 21 agent files
        └── templates/
            └── starter/           # 49 template files
                ├── CLAUDE.md
                ├── GETTING-STARTED.md
                ├── README.md
                ├── CHANGELOG.md
                ├── docs/
                │   ├── project-brief.md
                │   ├── project/
                │   └── development/
                │       ├── conventions/    # 7 files
                │       ├── workflows/      # 9 files
                │       ├── templates/      # 12 files
                │       └── misc/           # 4 files
                └── pm/
```

## Recognition

Contributors are recognized through:
- Git commit attribution
- CHANGELOG.md entries for significant contributions
- Project documentation acknowledgments
- GitHub contributor list

---

Thank you for contributing to the AI Toolkit project! Your contributions help improve AI-assisted development for everyone.
