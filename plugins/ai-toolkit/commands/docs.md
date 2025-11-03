---
tags: ["documentation", "generation", "validation", "synchronization", "health"]
description: "Unified documentation management - generate, validate, sync, update, and analyze documentation"
argument-hint: "[\"natural language instruction\"]"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/README.md  # Documentation structure and organization
---

# /docs Command

**Purpose**: Unified documentation management through natural language. Generate, validate, sync, update, and analyze project documentation using AI.

## Philosophy

Natural language describes the intent - AI determines the appropriate action. No need for separate commands for each documentation operation.

## Usage

```bash
# Generate documentation
/docs "generate API documentation for the auth system"
/docs "create technical architecture overview"
/docs "generate comprehensive project documentation"

# Validate documentation
/docs "validate all documentation links"
/docs "check for broken references and outdated content"
/docs "verify documentation accuracy"

# Sync with code
/docs "update documentation to reflect recent code changes"
/docs "sync API docs with latest endpoint changes"
/docs "align documentation with current codebase"

# Update and maintain
/docs "update outdated information across project docs"
/docs "comprehensive accuracy review"
/docs "fix all cross-references"

# Health analysis
/docs "analyze documentation health and coverage"
/docs "check documentation completeness"
/docs "generate documentation metrics report"
```

## How It Works

The AI analyzes your natural language instruction and determines the appropriate action:

**Generation Tasks**: Creates new documentation based on code analysis
- Analyzes codebase structure
- Generates appropriate documentation format
- Follows project documentation standards
- Creates comprehensive, accurate content

**Validation Tasks**: Checks documentation quality and accuracy
- Validates internal and external links
- Checks code examples and references
- Verifies documentation freshness
- Identifies outdated or missing content

**Synchronization Tasks**: Keeps docs aligned with code
- Analyzes recent code changes
- Updates affected documentation
- Maintains consistency between code and docs
- Preserves documentation structure

**Update Tasks**: Maintains documentation accuracy
- Reviews entire documentation tree
- Updates stale information
- Fixes broken references
- Ensures current project state reflected

**Health Analysis**: Provides documentation metrics
- Coverage analysis
- Quality scoring
- Freshness metrics
- Completeness assessment
- Actionable recommendations

## Agent Coordination

**Primary**: technical-writer (documentation creation and maintenance)
**Supporting**: context-analyzer (documentation health analysis), code-reviewer (accuracy validation)
**Domain Specialists**: Frontend/backend specialists for technical accuracy
**Security**: security-auditor (validates accuracy of security documentation, threat models, auth flows)

## Output Locations

Documentation follows project structure:
- **Project docs**: `docs/project/` - Architecture, decisions, features
- **Development docs**: `docs/development/` - Guidelines, standards, setup
- **AI toolkit docs**: `docs/ai-toolkit/` - Plugin documentation (read-only from plugin)

## Best Practices

**Be Specific**: "Generate API docs for auth endpoints" vs. "make some docs"

**Provide Context**: "Update database docs after schema migration" vs. "update docs"

**Set Scope**: "Validate links in docs/project/" vs. "check everything"

**Define Depth**: "Quick health check" vs. "comprehensive analysis with metrics"

## Examples by Task Type

### Generation Examples
```bash
/docs "generate complete API reference documentation"
/docs "create architecture decision record for the new caching layer"
/docs "generate database schema documentation with ER diagrams"
/docs "create user guide for the authentication system"
```

### Validation Examples
```bash
/docs "validate all markdown links in docs/"
/docs "check for broken code examples in technical documentation"
/docs "verify all external references are still valid"
/docs "validate documentation before commit"
```

### Synchronization Examples
```bash
/docs "sync documentation with code changes from last week"
/docs "update API docs after refactoring the user service"
/docs "align architecture docs with current system design"
/docs "sync all documentation with latest main branch"
```

### Update Examples
```bash
/docs "update all outdated version numbers in documentation"
/docs "refresh technology stack documentation"
/docs "update setup guides with new dependencies"
/docs "comprehensive documentation accuracy review"
```

### Health Analysis Examples
```bash
/docs "analyze documentation coverage and quality"
/docs "generate documentation health dashboard"
/docs "identify documentation gaps and missing content"
/docs "create documentation metrics report for team review"
```

## Integration

**Git Integration**: Documentation changes tracked in version control
**Code Analysis**: Analyzes actual codebase for accurate documentation
**Project Standards**: Follows project's documentation guidelines
**Living Documentation**: Documentation evolves with the project

## Quality Standards

- **Accuracy**: Documentation reflects actual code behavior
- **Completeness**: All public APIs and features documented
- **Freshness**: Documentation updated with code changes
- **Clarity**: Clear, concise, well-structured content
- **Examples**: Code examples tested and accurate
- **Links**: All references valid and current

All functionality preserved - natural language determines the action.
