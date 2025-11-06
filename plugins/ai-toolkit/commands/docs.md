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

**WHAT**: Unified documentation management - generate, validate, sync, update, and analyze documentation.

**WHY**: Maintain documentation accuracy, completeness, and freshness alongside code evolution.

**HOW**: See docs/development/README.md for documentation structure. Natural language intent determines action (generate/validate/sync/update/analyze).

## Philosophy

Natural language describes intent - AI determines action. No separate commands needed.

## Usage

```bash
# Generate
/docs "generate API documentation for auth system"
/docs "create technical architecture overview"

# Validate
/docs "validate all documentation links"
/docs "check for broken references and outdated content"

# Sync with code
/docs "update docs to reflect recent code changes"
/docs "sync API docs with latest endpoint changes"

# Update and maintain
/docs "update outdated information across project docs"
/docs "fix all cross-references"

# Health analysis
/docs "analyze documentation health and coverage"
/docs "generate documentation metrics report"
```

## How It Works

AI analyzes natural language and executes appropriate action:

**Generation**: Creates new documentation
- Analyzes codebase structure
- Generates appropriate format
- Follows project standards
- Creates comprehensive content

**Validation**: Checks quality and accuracy
- Validates internal/external links
- Checks code examples and references
- Verifies freshness
- Identifies outdated/missing content

**Synchronization**: Keeps docs aligned with code
- Analyzes recent code changes
- Updates affected documentation
- Maintains consistency
- Preserves structure

**Update**: Maintains accuracy
- Reviews documentation tree
- Updates stale information
- Fixes broken references
- Ensures current state reflected

**Health Analysis**: Provides metrics
- Coverage analysis
- Quality scoring
- Freshness metrics
- Completeness assessment
- Actionable recommendations

## Agent Coordination

**Primary**: technical-writer (documentation creation/maintenance)
**Supporting**: context-analyzer (health analysis), code-reviewer (accuracy validation)
**Domain**: Frontend/backend specialists (technical accuracy)
**Security**: security-auditor (validates security docs, threat models, auth flows)

## Output Locations

- **Project docs**: `docs/project/` - Architecture, decisions, features
- **Development docs**: `docs/development/` - Guidelines, standards, setup
- **AI toolkit docs**: `docs/ai-toolkit/` - Plugin docs (read-only from plugin)

## Best Practices

**Be Specific**: "Generate API docs for auth endpoints" vs. "make some docs"
**Provide Context**: "Update database docs after schema migration" vs. "update docs"
**Set Scope**: "Validate links in docs/project/" vs. "check everything"
**Define Depth**: "Quick health check" vs. "comprehensive analysis with metrics"

## Examples

**Generation:**
```bash
/docs "generate complete API reference documentation"
/docs "create ADR for new caching layer"
/docs "generate database schema documentation with ER diagrams"
```

**Validation:**
```bash
/docs "validate all markdown links in docs/"
/docs "check for broken code examples"
/docs "verify all external references still valid"
```

**Synchronization:**
```bash
/docs "sync docs with code changes from last week"
/docs "update API docs after refactoring user service"
/docs "align architecture docs with current design"
```

**Update:**
```bash
/docs "update all outdated version numbers"
/docs "refresh technology stack documentation"
/docs "comprehensive documentation accuracy review"
```

**Health Analysis:**
```bash
/docs "analyze documentation coverage and quality"
/docs "identify documentation gaps and missing content"
/docs "create documentation metrics report"
```

## Integration

**Git**: Documentation changes tracked in version control
**Code Analysis**: Analyzes actual codebase for accuracy
**Standards**: Follows project documentation guidelines
**Living Docs**: Evolves with the project

## Quality Standards

- **Accuracy**: Reflects actual code behavior
- **Completeness**: All public APIs/features documented
- **Freshness**: Updated with code changes
- **Clarity**: Clear, concise, well-structured
- **Examples**: Code examples tested and accurate
- **Links**: All references valid and current
