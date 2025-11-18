---
tags: ["workflow", "quality", "assessment", "validation"]
description: "Comprehensive quality assessment with multi-agent coordination"
argument-hint: "[--focus AREA]"
allowed-tools: ["Read", "Write", "Edit", "MultiEdit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/quality-gates.md  # Quality dimensions, thresholds, validation rules
---

# /quality Command

**WHAT**: Comprehensive quality assessment across code quality, security, performance, testing, and documentation.

**WHY**: Ensure consistent quality standards throughout development with multi-agent analysis and actionable recommendations.

**HOW**: Coordinate specialized agents (code-reviewer, security-auditor, performance-optimizer, test-engineer) to analyze the codebase and provide improvement recommendations.

## Usage

```bash
/quality                        # Comprehensive quality assessment (all dimensions)
/quality --focus security       # Focus on security analysis
/quality --focus performance    # Focus on performance analysis
/quality --focus testing        # Focus on test coverage and quality
```

## How It Works

1. **Read Quality Configuration** - Load quality dimensions and thresholds from `docs/development/workflows/quality-gates.md`
2. **Analyze Codebase** - Coordinate specialized agents based on focus area or run comprehensive analysis
3. **Generate Report** - Provide actionable recommendations with priority levels
4. **Suggest Improvements** - Offer specific fixes and refactoring suggestions

## Quality Dimensions

**Default dimensions** (configured in `quality-gates.md`):

- **Code Quality** - Maintainability, complexity, best practices (code-reviewer)
- **Security** - Vulnerabilities, OWASP compliance (security-auditor)
- **Performance** - Bottlenecks, optimization opportunities (performance-optimizer)
- **Testing** - Coverage, test quality, effectiveness (test-engineer)
- **Documentation** - Completeness, accuracy (technical-writer)

## Focus Areas

Use `--focus` to target specific quality dimensions:

- `security` - OWASP Top 10, vulnerabilities, auth/data protection
- `performance` - N+1 queries, inefficient algorithms, bottlenecks
- `testing` - Coverage analysis, test quality, missing tests
- `code` - Code quality, complexity, maintainability
- `docs` - Documentation completeness and accuracy

**No flag = Comprehensive analysis across all dimensions**

## When to Use

**During Development:**
- Before merging to staging/production
- After completing a task
- When quality concerns arise

**Regular Checks:**
- Weekly quality reviews
- Pre-release validation
- After major refactoring

**Targeted Analysis:**
- Security review before auth changes
- Performance check after data layer changes
- Test coverage validation after feature addition

## Example Output

```
Quality Assessment Report
=========================

Code Quality: 87/100 ✅
- 3 high-complexity functions identified
- Recommendation: Refactor UserService.validateCredentials()

Security: 92/100 ✅
- 1 medium-severity issue: SQL injection risk in search endpoint
- Recommendation: Use parameterized queries

Performance: 78/100 ⚠️
- N+1 query detected in /api/users endpoint
- Recommendation: Add eager loading for user.posts

Testing: 85/100 ✅
- Coverage: 82% (target: 80%)
- 12 untested edge cases identified

Documentation: 90/100 ✅
- API endpoints documented
- Missing: Error response examples

Overall: 86/100 ✅
Priority Actions:
1. Fix SQL injection in search (CRITICAL)
2. Optimize /api/users N+1 query (HIGH)
3. Refactor high-complexity functions (MEDIUM)
```

## Benefits

✅ **Single Command**: No complex subcommands or flags
✅ **Comprehensive by Default**: Analyzes all quality dimensions
✅ **Targeted When Needed**: Optional focus for specific concerns
✅ **Actionable Output**: Specific recommendations with priority
✅ **Multi-Agent Coordination**: Leverages specialized domain experts
✅ **Configuration-Driven**: Adapts to your quality standards via quality-gates.md

## Integration

**With `/implement`**: Quality checks run automatically during implementation phases

**With `/sanity-check`**: Use for quick quality validation mid-development

**With `/security-audit`**: For deeper OWASP compliance and penetration testing

**Before `/branch merge`**: Run comprehensive quality check before merging to staging/production
