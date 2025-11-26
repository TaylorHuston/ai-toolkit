---
tags: ["documentation", "generation", "validation", "synchronization", "health"]
description: "Unified documentation management - generate, validate, sync, update, and analyze documentation"
argument-hint: "--sync | --validate | --health | --update | --generate TARGET | \"natural language\""
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/README.md  # Documentation structure and organization
---

# /docs Command

**WHAT**: Unified documentation management - generate, validate, sync, update, and analyze documentation.

**WHY**: Maintain documentation accuracy, completeness, and freshness alongside code evolution.

**HOW**: Use flags for common operations, or natural language for specific requests.

## Usage

```bash
# Quick flags for common operations
/docs --sync                    # Sync docs with recent code changes
/docs --validate                # Check links and references
/docs --health                  # Documentation coverage and quality metrics
/docs --update                  # Update stale information across all docs
/docs --generate "auth system"  # Generate docs for specific target

# Natural language for specific requests
/docs "generate API documentation for the user service"
/docs "sync API docs with latest endpoint changes"
```

## Flags

| Flag | Purpose |
|------|---------|
| `--sync` | Analyze recent git changes, update affected documentation |
| `--validate` | Check all links, references, and code examples for accuracy |
| `--health` | Generate coverage metrics, quality scores, freshness report |
| `--update` | Review all docs, fix stale information and broken references |
| `--generate TARGET` | Generate documentation for specified target (module, API, feature) |

## Natural Language

For more specific requests, use natural language:

```bash
/docs "generate database schema documentation with ER diagrams"
/docs "validate only the API documentation"
/docs "sync docs with changes from the last 5 commits"
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
**Supporting**: code-reviewer (accuracy validation)
**Domain**: Frontend/backend specialists (technical accuracy)
**Security**: security-auditor (validates security docs, threat models, auth flows)

## Output Locations

- **Project docs**: `docs/project/` - Architecture, decisions, features
- **Development docs**: `docs/development/` - Guidelines, standards, setup

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

## Quality Standards

- **Accuracy**: Reflects actual code behavior
- **Completeness**: All public APIs/features documented
- **Freshness**: Updated with code changes
- **Clarity**: Clear, concise, well-structured
- **Examples**: Code examples tested and accurate
- **Links**: All references valid and current
