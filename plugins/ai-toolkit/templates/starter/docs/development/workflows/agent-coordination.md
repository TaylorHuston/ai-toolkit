---
last_updated: "2025-11-26"
description: "Agent coordination patterns, governance rules, and collaboration protocols"

# === Agent Coordination Configuration ===

# Architectural Governance
architectural_governance:
  mandatory_review_agent: "code-architect"
  review_trigger: "all_plans"
  review_authority: "blocking"

# Security Governance
security_governance:
  review_agent: "security-auditor"
  auto_invocation: true
  trigger_mode: "conditional"
  security_keywords:
    - auth, login, password, token, session, permission, role
    - encrypt, decrypt, hash, crypto, PII, sensitive data
    - security, vulnerability, OWASP, XSS, CSRF, SQL injection
    - admin, privilege, API key, secret, credential, OAuth
  security_file_patterns:
    - "**/auth/**", "**/security/**", "**/crypto/**"
    - "**/*Auth*", "**/*Security*", "**/guards/**"
  review_authority: "blocking"

# Quality Governance
quality_gates:
  code_review_agent: "code-reviewer"
  minimum_score: 90
  review_frequency: "per_phase"
  test_coverage_agent: "domain_specialists"
  test_strategy_agent: "test-engineer"
  minimum_coverage: 95

# Escalation Hierarchy
escalation_hierarchy:
  architectural_questions:
    from: ["domain_specialists", "refactoring-specialist"]
    to: "code-architect"
  security_concerns:
    from: ["domain_specialists", "code-reviewer"]
    to: "security-auditor"
  performance_issues:
    from: ["database-specialist", "frontend-specialist", "backend-specialist"]
    to: "performance-optimizer"
  refactoring_needs:
    from: ["code-reviewer", "migration-specialist"]
    to: "refactoring-specialist"

# Collaboration Patterns
collaboration_patterns:
  parallel: [frontend-specialist, backend-specialist]
  sequential: [domain_specialists → code-reviewer → technical-writer]
  continuous: [domain_specialists + security-auditor]
---

# Agent Coordination Guidelines

**Referenced by:** All agent files

## Governance Rules

### Code-Architect (Mandatory)

**All plans reviewed before user presentation.**

- **Trigger**: `/plan` command (automatic, non-skippable)
- **Authority**: BLOCKING - can prevent plan presentation
- **Scope**: Architectural soundness, ADR consistency, technology choices, cross-cutting concerns

### Security-Auditor (Conditional)

**Auto-invoked for security-relevant work.**

- **Plan-Level**: Task contains security keywords → threat modeling review
- **Phase-Level**: Files match security patterns → implementation review
- **Authority**: BLOCKING - can prevent phase completion for critical issues
- **Detection**: Keywords and file patterns in YAML config above

### Code-Reviewer (Per-Phase)

**Every phase reviewed before completion.**

- **Trigger**: `/implement` iteration loop
- **Threshold**: 90+ score required (iterate until met)
- **Scope**: Code quality, best practices, maintainability, test coverage

### Test-Engineer (Guidance)

**Test strategy and coverage validation.**

- **Trigger**: `/plan` (strategy), `/implement` (guidance), `/troubleshoot` (debugging)
- **Threshold**: 95% coverage required

---

## Escalation Paths

**When to escalate to another agent:**

| Escalate To | From | When |
|-------------|------|------|
| code-architect | Any specialist | Architectural patterns, ADR applicability, system design, technology choices |
| security-auditor | Any specialist | Auth/crypto work, sensitive data, unclear security implications |
| performance-optimizer | Domain specialists | Cross-cutting performance, app-level optimization needed |
| refactoring-specialist | code-reviewer | Systematic refactoring, technical debt across multiple files |

---

## Decision Trees

### Performance Issues
```
Is it slow?
├─ Database queries? → database-specialist
├─ Frontend rendering? → frontend-specialist
├─ Backend API? → backend-specialist
├─ Infrastructure? → devops-engineer
└─ Cross-cutting? → performance-optimizer
```

### Code Quality Issues
```
Code needs improvement?
├─ Design patterns? → refactoring-specialist
├─ Architecture? → code-architect
├─ Testing? → test-engineer
├─ Security? → security-auditor
└─ General review? → code-reviewer
```

### Documentation Needs
```
Documentation required?
├─ Architecture decisions? → code-architect (ADRs)
├─ API documentation? → technical-writer
├─ Security docs? → security-auditor + technical-writer
└─ User guides? → technical-writer
```

---

## Collaboration Patterns

**Parallel Work:**
- Frontend + Backend when API contract defined
- Domain specialists + code-reviewer (tests alongside implementation)

**Sequential Handoff:**
- Domain specialist → code-reviewer (iterate until 90+) → phase complete
- code-reviewer → technical-writer (documentation after approval)

**Continuous:**
- Domain specialists + security-auditor (security-relevant work)
- Domain specialists + test-engineer (test strategy guidance)

---

## Responsibility Boundaries

| Domain | Primary Agent | Escalate To |
|--------|---------------|-------------|
| Query optimization | database-specialist | performance-optimizer (if app-level) |
| Code quality | refactoring-specialist | - |
| Framework upgrades | migration-specialist | refactoring-specialist (post-migration) |
| Analytics features | backend-specialist | data-analyst (analysis) |

---

## Agent Handoff in Practice

**Example: `/implement` orchestrating a backend phase**

```
User: /implement 001 1.2

/implement command:
1. Reads PLAN.md → Phase 1.2: "Implement login endpoint"
2. Determines domain: backend → Selects backend-specialist
3. Invokes backend-specialist

backend-specialist:
4. Reads WORKLOG.md for context
5. Follows TDD loop: tests → implement → tests pass
6. Writes WORKLOG.md entry

/implement command:
7. Invokes code-reviewer

code-reviewer:
8. Reviews → Score: 85 ("Add input validation")

/implement command:
9. Score < 90 → Re-invokes backend-specialist

backend-specialist:
10. Addresses feedback, re-runs tests

/implement command:
11. Re-invokes code-reviewer → Score: 92 ✓
12. Marks phase complete, updates PLAN.md
```

**Key insight**: Commands orchestrate, agents execute. WORKLOG enables handoffs.

---

## Context-Analyzer

**On-demand external research specialist.**

**Use For:**
- External documentation lookup
- Community solutions research
- Best practice discovery

**Don't Use For:**
- Local project context (use Read/Grep/Glob)
- ADR discovery (use Glob)
- Codebase patterns (use Read directly)

**Workflow:**
1. Checks CLAUDE.md Resources first
2. Fetches docs via Context7
3. Searches community via WebSearch
4. Returns curated summary with links
5. Creates Investigation WORKLOG entry

---

## Related Files

- task-workflow.md - TDD loop, quality gates
- security-guidelines.md - OWASP compliance
- architectural-principles.md - ADR patterns
- worklog-format.md - Entry formats
