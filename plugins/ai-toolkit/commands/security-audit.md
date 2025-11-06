---
tags: ["workflow", "security", "audit", "compliance"]
description: "OWASP-compliant security assessment with vulnerability remediation"
argument-hint: "[--scope SCOPE] [--depth DEPTH] [--compliance FRAMEWORK] [--output FORMAT]"
allowed-tools: ["Read", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/guidelines/security-guidelines.md  # Security practices and compliance requirements
---

# /security-audit Command

**WHAT**: OWASP-compliant security assessment with vulnerability identification and remediation guidance.

**WHY**: Proactively identify and fix security vulnerabilities before they reach production, ensuring compliance with security standards.

**HOW**: See security-guidelines.md for security practices. Multi-agent assessment (security-auditor, code-reviewer) scans OWASP Top 10, auth/authz, data protection, and infrastructure.

## Usage

```bash
/security-audit                      # Comprehensive security audit
/security-audit --scope code         # Code-focused security review
/security-audit --depth comprehensive # Deep security analysis
/security-audit --compliance GDPR    # Compliance-focused audit
```

## Process

Comprehensive security assessment workflow:
1. Scan for common security vulnerabilities (OWASP Top 10)
2. Analyze authentication and authorization implementation
3. Review data protection and privacy compliance
4. Assess infrastructure and deployment security
5. Generate detailed security report with remediation guidance

## Agent Coordination

**Primary**: security-auditor (for comprehensive vulnerability assessment and OWASP compliance)
**Supporting**: code-reviewer (security-focused code review), database-specialist (data security), devops-engineer (infrastructure security)

## Options

- `--scope`: Audit scope (code, infrastructure, data, all)
- `--depth`: Audit depth (basic, standard, comprehensive)
- `--compliance`: Compliance framework (GDPR, PCI-DSS, HIPAA, SOC2)
- `--output`: Report format (summary, detailed, checklist)

## Process

- Use security-auditor for comprehensive vulnerability assessment
- Use code-reviewer for security-focused code review
- Use database-specialist for data security review (if applicable)
- Run automated security scanning tools
- Generate prioritized vulnerability report
- Provide specific remediation recommendations
- Update security documentation and procedures

## Examples

**Full audit**: `/security-audit` → Comprehensive OWASP assessment → Vulnerability report
**Code focus**: `/security-audit --scope code` → Code security review → Remediation guide
**Compliance**: `/security-audit --compliance GDPR` → GDPR compliance assessment → Action plan