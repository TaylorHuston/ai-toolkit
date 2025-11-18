---
tags: ["workflow", "quality", "assessment", "validation"]
description: "Comprehensive quality assessment with multi-agent coordination"
argument-hint: "[assess|validate|audit|fix] [--scope SCOPE] [--depth DEPTH] [--focus FOCUS]"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/development-loop.md  # Quality dimensions, quality gates, thresholds
---

# /quality Command

**WHAT**: Comprehensive quality assessment with multi-agent coordination across code, security, performance, and testing.

**WHY**: Ensure quality throughout development lifecycle with automated validation, intelligent remediation, and progressive quality gates.

**HOW**: See development-loop.md for quality dimensions and gates. Multi-agent orchestration (assess/validate/audit/fix) with context-aware checks.

## Usage

```bash
/quality assess                      # Comprehensive quality assessment
/quality validate --scope current-phase  # Phase-specific validation
/quality audit --focus security      # Security-focused audit
/quality fix --scope code-style      # Auto-fix quality issues
/quality validate --scope commit-ready   # Pre-commit check
```

## Commands

### `assess` - Comprehensive Quality Assessment

Multi-dimensional quality analysis using specialized agents.

**Options:**
- `--scope all|current|specific-files`
- `--depth shallow|standard|deep`
- `--focus code|security|performance|architecture`

**Agents:**
1. **code-reviewer** - Code quality, maintainability, best practices
2. **security-auditor** - Security vulnerabilities, compliance
3. **performance-optimizer** - Performance bottlenecks, optimization
4. **test-engineer** - Test coverage, quality, effectiveness

### `validate` - Quality Gate Validation

Validates quality gates with automatic remediation suggestions.

**Options:**
- `--scope current-phase|all-phases|commit-ready`
- `--auto-fix` - Auto-fix simple issues
- `--report FORMAT` - Generate validation report

**Validates:**
- Multi-phase quality (P1, P2, P3)
- Test coverage thresholds
- Code quality metrics
- Documentation completeness
- Security compliance

**Agents:**
- **code-reviewer** - Code quality validation
- **test-engineer** - Coverage analysis
- **security-auditor** - Security compliance

### `audit` - Security and Compliance Audit

OWASP compliance and vulnerability assessment.

**Options:**
- `--focus security|compliance|vulnerabilities`
- `--framework owasp|nist|custom`
- `--severity low|medium|high|critical`

**Checks:**
- OWASP Top 10 compliance
- Dependency vulnerabilities
- Security code patterns
- Infrastructure security

**Agents:**
- **security-auditor** (primary)
- **code-reviewer** (code patterns)
- **devops-engineer** (infrastructure)

### `fix` - Automated Issue Resolution

Agent-guided issue resolution.

**Options:**
- `--scope code-style|tests|security|documentation`
- `--auto` - Auto-fix safe issues
- `--interactive` - Interactive approval
- `--dry-run` - Preview fixes

**Fixes:**
- Code style and formatting
- Test failures
- Documentation sync
- Simple security issues

**Agents:**
- **refactoring-specialist** - Code improvements
- **test-engineer** - Test fixes
- **technical-writer** - Documentation fixes

## Quality Dimensions

Configuration in `docs/development/workflows/development-loop.md` (YAML frontmatter).

**Default Dimensions** (customizable):
- **code_quality** - Complexity, maintainability (code-reviewer)
- **security** - Vulnerabilities, compliance (security-auditor)
- **performance** - Speed, efficiency (performance-optimizer)
- **testing** - Coverage, effectiveness (test-engineer)
- **documentation** - Completeness, accuracy (technical-writer)
- **architecture** - Design patterns (code-architect)

**Customization**: Teams adjust based on priorities (startups drop docs, regulated industries add compliance)

## Workflow Integration

Read `development-loop.md` for current quality gate configuration.

**During `/implement`**: Auto-validation between phases, progressive gate enforcement
**Pre-Commit**: Comprehensive check, fix suggestions, AI-driven remediation
**Continuous**: Background monitoring, proactive detection, trend analysis

## Examples

**Comprehensive assessment:**
```bash
/quality assess --depth deep
# → code-reviewer, security-auditor, performance-optimizer, test-engineer
```

**Pre-commit check:**
```bash
/quality validate --scope commit-ready
# → All quality gates validated, pass/fail with remediation
```

**Security audit:**
```bash
/quality audit --focus security --framework owasp
# → OWASP Top 10 compliance, vulnerability assessment
```

**Auto-fix:**
```bash
/quality fix --scope code-style --auto
# → Identify and apply safe formatting fixes
```

## Benefits

✅ **Unified Interface**: Single command for all quality concerns
✅ **Intelligent Coordination**: Agents work together
✅ **Context Awareness**: Understands project state
✅ **Automated Remediation**: Intelligent fixes
✅ **Progressive Quality**: Gates throughout development