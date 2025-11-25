---
last_updated: "2025-11-02"
description: "Agent coordination patterns, governance rules, and collaboration protocols"

# === Agent Coordination Configuration ===

# Architectural Governance
architectural_governance:
  mandatory_review_agent: "code-architect"
  review_trigger: "all_plans"  # Every PLAN.md reviewed before user presentation
  review_authority: "blocking"  # Can block plan presentation until approved
  adherence_requirement: "Must follow existing ADRs and architectural patterns"

# Security Governance
security_governance:
  review_agent: "security-auditor"
  auto_invocation: true
  trigger_mode: "conditional"  # Auto-detect security-relevant work

  # Detection Criteria (from development-loop.md)
  security_keywords:
    - auth, login, password, token, session, permission, role, access control
    - encrypt, decrypt, hash, salt, crypto, PII, sensitive data, personal information
    - security, vulnerability, threat, OWASP, XSS, CSRF, SQL injection, sanitize, validate input
    - admin, privilege, elevation, sudo, root
    - API key, secret, credential, OAuth, SAML, SSO, third-party

  security_file_patterns:
    - "**/auth/**", "**/authentication/**", "**/authorization/**"
    - "**/security/**", "**/crypto/**", "**/encryption/**"
    - "**/*Auth*", "**/*Security*", "**/*Crypto*", "**/*Validation*"
    - "**/middleware/auth*", "**/guards/**", "**/policies/**"

  review_authority: "blocking"  # Can block phase completion for critical issues
  review_scope: "OWASP compliance, threat modeling, input validation, crypto usage"

# Quality Governance
quality_gates:
  code_review_agent: "code-reviewer"
  minimum_score: 90  # Per-phase threshold (configurable in development-loop.md)
  review_frequency: "per_phase"  # Every phase reviewed before completion

  test_coverage_agent: "domain_specialists"  # backend-specialist, frontend-specialist write domain tests
  test_strategy_agent: "test-engineer"  # Provides strategy, framework setup, complex scenarios
  minimum_coverage: 95  # Percentage (configurable in development-loop.md)
  test_strategy: "test_first"  # TDD/BDD encouraged

# Agent Escalation Paths
escalation_hierarchy:
  architectural_questions:
    from: ["domain_specialists", "refactoring-specialist"]
    to: "code-architect"
    when: "Uncertain about architectural patterns, ADR applicability, system design"

  security_concerns:
    from: ["domain_specialists", "code-reviewer"]
    to: "security-auditor"
    when: "Security implications unclear, handling sensitive data, auth/crypto work"

  performance_issues:
    from: ["database-specialist", "frontend-specialist", "backend-specialist"]
    to: "performance-optimizer"
    when: "Cross-cutting performance concerns, application-level optimization needed"

  refactoring_needs:
    from: ["code-reviewer", "migration-specialist"]
    to: "refactoring-specialist"
    when: "Code quality issues, technical debt, pattern improvements needed"

# Agent Collaboration Patterns
collaboration_patterns:
  parallel_work:
    - [frontend-specialist, backend-specialist]  # Can work simultaneously on features
    - [domain_specialists, code-reviewer]  # Domain specialists write tests alongside implementation

  sequential_handoff:
    - [domain_specialists, code-reviewer]  # Implementation reviewed after completion
    - [code-reviewer, technical-writer]  # Documentation after code approved

  continuous_collaboration:
    - [domain_specialists, test-engineer]  # Ongoing test-first guidance
    - [domain_specialists, security-auditor]  # Security review during implementation

---

# Agent Coordination Guidelines

**Referenced by:** All agent files for understanding governance, escalation, and collaboration

## Quick Reference

This guideline defines how agents coordinate, when to escalate, and how quality gates enforce standards. Agents read this to understand their role in the broader workflow.

## Architectural Governance

### Code-Architect: Mandatory Review Authority

**All implementation plans reviewed by code-architect before user presentation.**

**When Invoked:**
- **Automatic** by `/plan` command (mandatory, non-skippable)
- After project-manager generates initial PLAN.md
- Before plan shown to user

**Review Scope:**
- Architectural soundness and consistency with existing ADRs
- Phase structure and logical breakdown
- Technology choices and patterns
- Scalability and maintainability considerations
- Cross-cutting concerns (security, performance, observability)
- Integration with existing system architecture

**Authority:**
- **BLOCKING**: Can prevent plan presentation until issues resolved
- **ADVISORY**: Can suggest additional phases or approach changes
- **ADR CREATION**: Can require ADR for significant architectural decisions

**What This Means for Domain Specialists:**
1. Your implementation approach will be architecturally reviewed
2. Follow existing ADRs and architectural patterns
3. When uncertain about architecture, escalate to code-architect
4. Don't make significant architectural decisions without code-architect involvement

**Configuration:**
- Review requirements defined in `development-loop.md` "Plan Review Requirements"
- ADR patterns defined in `architectural-principles.md`

## Security Governance

### Security-Auditor: Conditional Auto-Invocation

**Security-relevant work automatically reviewed by security-auditor.**

**When Invoked:**

**1. Plan-Level Review** (after code-architect approval):
- **Trigger**: Task description or acceptance criteria contain security keywords
- **Timing**: During `/plan` command, after code-architect approves
- **Scope**: Threat modeling, security approach validation, required security phases

**2. Phase-Level Review** (before phase completion):
- **Trigger**: Phase description or modified files match security patterns
- **Timing**: During `/implement` command, before marking phase complete
- **Scope**: Implementation review for vulnerabilities, crypto usage, input validation

**Detection Criteria** (see machine-readable config above):
- **Keywords**: auth, login, password, token, encrypt, crypto, PII, OAuth, admin, etc.
- **File Patterns**: `**/auth/**`, `**/security/**`, `**/*Auth*`, `**/guards/**`, etc.

**Authority:**
- **BLOCKING**: Can prevent phase completion for critical vulnerabilities
- **ADVISORY**: Can recommend additional security controls
- **PHASE REQUIREMENT**: Can require threat modeling or penetration testing phases

**What This Means for Domain Specialists:**
1. Security-auditor will automatically review your work if security-relevant
2. You don't need to manually invoke - auto-detection handles it
3. Critical security issues will block phase completion
4. Follow OWASP guidelines and secure coding practices
5. When implementing auth/crypto/permissions, expect security review

**Configuration:**
- Detection criteria defined in `development-loop.md` "Security-Relevant Phase Detection"
- Security standards defined in `security-guidelines.md`
- OWASP compliance patterns in security-auditor agent

**Manual Override:**
- If security work not auto-detected, manually request security review
- User can explicitly invoke `/security-audit` for comprehensive review

## Quality Gates

### Code-Reviewer: Per-Phase Quality Validation

**Every implementation phase reviewed for code quality before completion.**

**When Invoked:**
- During `/implement` command iteration loop
- After domain specialist completes implementation
- Before marking phase as complete in PLAN.md

**Review Scope:**
- Code quality and best practices
- Design patterns appropriateness
- Maintainability and readability
- Performance implications
- Test coverage adequacy

**Quality Threshold:**
- **Minimum Score**: 90/100 (configurable in `development-loop.md`)
- **Enforcement**: Iterate until threshold met
- **Guidance**: Constructive feedback for improvements

**What This Means for Domain Specialists:**
1. Your code will be reviewed every phase
2. Aim for 90+ quality score to avoid rework
3. Follow coding standards and best practices
4. Write maintainable, well-documented code

### Test-Engineer: Test-First Guidance

**Test strategy and coverage validated throughout implementation.**

**When Involved:**
- During `/plan` command (test strategy in phases)
- During `/implement` command (test-first guidance)
- Via `/troubleshoot` command (test failure debugging and resolution)

**Test Coverage Threshold:**
- **Minimum Coverage**: 95% (configurable in `development-loop.md`)
- **Test-First**: Encouraged via pragmatic test-first protocol
- **Enforcement**: Coverage checked before phase completion

**What This Means for Domain Specialists:**
1. Consider tests when planning implementation
2. Write tests before or alongside implementation
3. Aim for 95%+ coverage
4. test-engineer provides guidance on test strategy

## Agent Escalation Patterns

**When to escalate to other agents:**

### Escalate to Code-Architect

**From:** Any domain specialist, refactoring-specialist
**When:**
- Uncertain about architectural patterns or ADR applicability
- Considering significant architectural changes
- Need guidance on system design or technology choices
- Cross-cutting concerns affecting multiple modules
- Technical debt has architectural implications

**Example:**
```
Frontend-specialist: "Should this state management be Redux or Context API?"
→ Escalate to code-architect for architectural guidance
```

### Escalate to Security-Auditor

**From:** Any domain specialist, code-reviewer
**When:**
- Implementing authentication or authorization logic
- Handling sensitive data (PII, credentials, payment info)
- Using cryptography or encryption
- Unsure about input validation or sanitization approach
- Security implications unclear

**Note:** Often auto-invoked, but manual escalation available.

**Example:**
```
Backend-specialist: "Storing API keys - use environment variables or secrets manager?"
→ Escalate to security-auditor for security best practices
```

### Escalate to Performance-Optimizer

**From:** database-specialist, frontend-specialist, backend-specialist, devops-engineer
**When:**
- Cross-cutting performance issues (spans multiple components)
- Application-level optimization needed (not just database or frontend)
- Profiling required to identify bottlenecks
- Performance targets not met despite domain-level optimization

**Handoff Pattern:**
- database-specialist handles query optimization first
- If still slow, escalate to performance-optimizer for application-level analysis

**Example:**
```
Database-specialist: "Queries optimized with indexes, but page still slow"
→ Escalate to performance-optimizer for broader performance analysis
```

### Escalate to Refactoring-Specialist

**From:** code-reviewer, migration-specialist, any domain specialist
**When:**
- Code quality issues require systematic refactoring
- Technical debt cleanup needed across multiple files
- Pattern improvements needed (not just spot fixes)
- Code smells identified but unclear how to refactor

**Example:**
```
Code-reviewer: "This module has high cyclomatic complexity across 15 functions"
→ Escalate to refactoring-specialist for systematic cleanup
```

## Agent Collaboration Patterns

### Parallel Work Patterns

**Frontend + Backend Simultaneously:**
- When features have clear API contracts defined
- Both can work in parallel once contract agreed
- API-designer can facilitate contract definition

**Test-Engineer + Domain Specialists:**
- Domain specialists write domain-specific tests (backend-specialist writes backend tests, frontend-specialist writes frontend tests)
- test-engineer provides test strategy, framework setup, and architecture guidance
- test-engineer handles complex cross-cutting test scenarios and test infrastructure
- Continuous: test-engineer available for test architecture questions and best practices

### Sequential Handoff Patterns

**Domain Specialists → Code-Reviewer:**
1. Domain specialist completes implementation
2. code-reviewer validates quality, best practices
3. Iterate until quality score ≥ 90
4. Mark phase complete

**Code-Reviewer → Technical-Writer:**
1. Code approved by code-reviewer
2. technical-writer documents new features
3. Ensures documentation accuracy matches implementation

### Continuous Collaboration Patterns

**Domain Specialists + Test-Engineer:**
- Domain specialists write domain-specific tests as part of implementation
- test-engineer available for test strategy, framework, and architecture questions
- test-engineer provides guidance on complex test scenarios and test infrastructure
- Continuous feedback on test coverage and quality

**Domain Specialists + Security-Auditor:**
- For security-relevant work, security-auditor reviews during implementation
- Can provide early feedback before phase completion
- Prevents late-stage security rework

## Agent Invocation Decision Tree

**Use this to decide which agent to invoke for edge cases:**

### Performance Issues
```
Is it slow?
├─ Database queries? → database-specialist (query optimization, indexes)
├─ Frontend rendering? → frontend-specialist (then performance-optimizer if needed)
├─ Backend API? → backend-specialist (then performance-optimizer if needed)
├─ Infrastructure? → devops-engineer (scaling, caching, CDN)
└─ Cross-cutting? → performance-optimizer (application-level profiling)
```

### Code Quality Issues
```
Code needs improvement?
├─ Design patterns? → refactoring-specialist (systematic cleanup)
├─ Architecture? → code-architect (system design)
├─ Testing? → test-engineer (test strategy, coverage)
├─ Security? → security-auditor (vulnerabilities, OWASP)
└─ General review? → code-reviewer (quality validation)
```

### Documentation Needs
```
Documentation required?
├─ Architecture decisions? → code-architect (creates ADRs)
├─ API documentation? → technical-writer (API docs, guides)
├─ Security docs? → security-auditor + technical-writer (threat models, auth flows)
└─ User guides? → technical-writer (end-user documentation)
```

### Migration/Upgrade Work
```
Upgrading framework or dependencies?
├─ Version upgrade only? → migration-specialist (dependency updates)
├─ + Code pattern changes? → migration-specialist + refactoring-specialist (parallel work)
├─ + Architecture changes? → code-architect + migration-specialist (architectural guidance)
└─ + Performance concerns? → migration-specialist + performance-optimizer (optimization)
```

## Responsibility Boundaries

**Clarifying overlapping domains:**

### Performance Optimization

**Database-Specialist Handles:**
- Query optimization (indexes, query structure)
- Database-level performance (connection pooling, caching)
- Schema design for performance

**Performance-Optimizer Handles:**
- Application-level performance (algorithmic optimization)
- Cross-cutting performance (multiple components)
- Profiling and bottleneck identification
- Performance testing strategy

**Handoff:** database-specialist optimizes database layer first, escalates to performance-optimizer if application-level issues remain.

### Code Improvement

**Refactoring-Specialist Handles:**
- Code quality improvements within current framework
- Technical debt reduction
- Pattern improvements and code smells

**Migration-Specialist Handles:**
- Framework version upgrades
- Dependency migrations
- Breaking API changes

**Handoff:** migration-specialist leads upgrades, refactoring-specialist handles code modernization post-migration.

### Analytics & Data

**Data-Analyst Handles:**
- Data processing and analysis
- Business intelligence and reporting
- Data visualization preparation

**Backend-Specialist Handles:**
- Analytics feature implementation (tracking, events)
- Data pipelines and ETL
- Analytics API endpoints

**Handoff:** backend-specialist implements analytics features, data-analyst analyzes resulting data.

## Context-Analyzer: External Research Specialist

**On-demand research for external knowledge needs.**

**Role**: External research specialist that curates signal from noise. When agents need outside information (documentation, community solutions, examples), context-analyzer researches extensively and returns focused summaries with curated resource links.

**When Invoked:**
- During `/troubleshoot` Research phase (external docs/solutions needed)
- When implementation agents need best practices research
- When exploring unfamiliar technologies or patterns
- Explicitly requested by user or agent for external knowledge

**What It Provides:**
- Curated documentation summaries (via Context7)
- Community solutions from blogs, Stack Overflow, GitHub (via WebSearch + WebFetch)
- Resource recommendations with quality filtering
- Investigation WORKLOG entries documenting findings
- Suggestions for adding exceptional resources to CLAUDE.md

**What It Does NOT Do:**
- ❌ Local project context gathering (agents read files directly using Read/Grep/Glob)
- ❌ Auto-invocation before tasks (only invoked on-demand)
- ❌ ADR or pattern discovery (use Grep/Glob for `docs/decisions/*.md`)
- ❌ Codebase exploration (use Serena tools or Read/Grep directly)

**Research Workflow:**
1. Checks CLAUDE.md Resources section first (project-curated links)
2. Fetches official documentation via Context7
3. Searches community solutions via WebSearch + WebFetch
4. Reads 10-20 sources, extracts signal, returns 2-5 page summary
5. Curates best resources with quality filtering
6. Creates Investigation WORKLOG entry with findings

**WORKLOG Format**: Investigation entries (see `worklog-format.md` for Investigation format)

**Key Benefit**: Research happens in context-analyzer's context window, implementation agents keep clean context focused on the problem.

**What This Means for Domain Specialists:**
1. Invoke context-analyzer when you need external research (not local project context)
2. Provide specific research query: "PostgreSQL JSONB aggregation performance solutions"
3. Expect curated summary with resource links, not raw research dump
4. Check CLAUDE.md Resources section first (context-analyzer checks there too)
5. Read Investigation WORKLOG entry for research findings and curated resources

**When to Invoke context-analyzer:**

✅ **External Documentation Research**:
- "How does React useEffect cleanup work?"
- "What's the PostgreSQL JSONB aggregation best practice?"
- "Official Next.js caching strategy documentation?"

✅ **Community Solutions**:
- "How do others solve rate limiting in distributed systems?"
- "Common patterns for Convex auth integration?"
- "Why is my JSONB query timing out - community solutions?"

✅ **Best Practice Discovery**:
- "What's the recommended approach for API versioning?"
- "OAuth2 PKCE flow implementation patterns?"
- "Node.js memory leak debugging techniques?"

❌ **Don't Invoke context-analyzer for**:
- Local project context (use Read/Grep/Glob tools directly)
- ADR discovery (use Glob for `docs/decisions/*.md`)
- Previous work review (read WORKLOG.md directly)
- Codebase patterns (use Serena tools: get_symbols_overview, find_symbol)
- Architecture questions (escalate to code-architect)

**Example Invocation**:
```
# Good - specific external research query
Domain specialist: "I need to research PostgreSQL JSONB aggregation performance issues causing timeouts"
→ Invoke context-analyzer with query

# Bad - local project context (do yourself)
Domain specialist: "What ADRs exist for caching?"
→ Use Grep: "grep -r 'caching' docs/decisions/*.md"

# Bad - too generic
Domain specialist: "Research the task"
→ Be specific about what external knowledge you need
```

## Customization

**Teams can customize governance rules in this guideline:**

### Adjust Quality Thresholds
```yaml
quality_gates:
  minimum_score: 85  # Lower threshold for faster iteration
  minimum_coverage: 90  # Adjust test coverage requirement
```

### Modify Security Detection
```yaml
security_governance:
  security_keywords:
    - Add custom keywords for your domain
    - Remove keywords that cause false positives

  security_file_patterns:
    - Add custom file patterns
    - "**/your-custom-security-path/**"
```

### Change Escalation Paths
```yaml
escalation_hierarchy:
  custom_escalation:
    from: ["your-custom-agent"]
    to: "target-agent"
    when: "Your custom escalation criteria"
```

## Related Documentation

**Governance Sources:**
- `development-loop.md` - Quality gates, test coverage, security detection criteria
- `security-guidelines.md` - Security standards and OWASP compliance
- `architectural-principles.md` - ADR patterns and architectural principles
- `git-workflow.md` - Branch merge gates and deployment validation
